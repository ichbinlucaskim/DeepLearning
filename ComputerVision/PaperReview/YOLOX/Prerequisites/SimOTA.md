Important things in OTA

![ota](https://miro.medium.com/v2/resize:fit:990/format:webp/1*T040dIc7iJL7Uhq_UkvdPw.png)

---

$$c^{fg} = c_{cls} + \alpha c_{reg} + c_{cp}$$

- loss/quality aware
- center prior
- dynamic number of positive anchors for each ground-truth
- global view
---

⭐️ The research found that solving OT problem via Sinkhorn-Knopp algo brings 25% extra training time, wichi is quite expensive for training 300 epochs.

So they simplify this. And this is SimOTA

---

$$c_{ij} = L^{cls}_{ij} + \lambda L^{reg}_{ij} $$

- $c_{ij}$ means cost function
- where $\lambda$ is a balancing coeffcient 
- $L^{cls}_{ij}$ and $L^{reg}_{ij}$ are *classification loss* and *regression loss* between gt $g_i$ and prediction $p_j$
- gt $g_i$ means the [[ground truth for the i-th]] object in your dataset.

---

⭐️ The algorithm looks like a lot to deal with, but it’s not that bad. SimOTA makes the algorithm much **simpler and faster**. 🤔 Maybe $c^{fg} > c_{ij}$

- Ref

https://medium.com/mlearning-ai/yolox-explanation-simota-for-dynamic-label-assignment-8fa5ae397f76

Instead of using Sinkhorn Iteration, SimOTA selects the top _kᵢ_ (or s_ᵢ_) predictions with the lowest cost as the positive samples for the _i_th ground truth object. Using the SimOTA method of assignment, a single iteration over all gt objects approximates the assignment instead of using an optimization algorithm to get the most optimal assignment.

The SimOTA algorithm looks like the following:

1. Assign _m_ and _n_ as the counts of the number of ground truths and number of anchors
2. Get the class predictions Pᶜˡˢ and the regression predictions Pᵇᵒˣ by sending image _I_ through the model.
3. Create the supplying vector, _s_, which has _m_ + 1 values. Use **dynamic _k_ estimation** to get the supply of each gt and store it in the vector.
4. _s_[_m_+1] = _n_ — sum(_s_), The background supply at location _m_ + 1 in the supplying vector is equal to the _n_ — sum(_s_)
5. Initialize the demanding vector, _d,_ as a vector of size _n_ filled with ones.
6. Get the pairwise _cls_ loss between each _j_th prediction and its corresponding _i_th ground truth label. _c_ᶜˡˢ = FocalLoss(Pᶜˡˢ, _G_ᶜˡˢ)
7. Get the pairwise _reg_ loss between each _j_th prediction and its corresponding _i_th ground truth label. _c_ʳᵉᵍ = IoULoss(Pᵇᵒˣ, _G_ᵇᵒˣ)
8. Get the pairwise **center prior** between each _j_th anchor and its corresponding _i_th gt. _c_ᶜᵖ = CenterPrior(A_ⱼ_, _G_ᵇᵒˣ)
9. Get the background class cost: _c_ᵇᵍ = FocalLoss(Pᶜˡˢ, Ø)
10. Get the foreground cost: _c_ᶠᵍ = _c_ᶜˡˢ + _αc_ʳᵉᵍ + _c_ᶜᵖ
11. Compute the final cost matrix _c_ by concatenating _c_ᵇᵍ to _c_ᶠᵍ to form a final matrix of shape (_m_+1, _n_)
12. Iterate over all supply _sᵢ_ in _s_ and get the top _sᵢ_ best predictions with the lowest cost _cᵢ_. The resulting array should have _m_ values where each _mᵢ_ index in the resulting array has at most _sᵢ_ predictions.
13. Return the resulting array

After running SimOTA, the output will be an array of size _m_ where each _i_th element in the resulting array is a positive labeled anchor/prediction corresponding to the _i_th ground truth _Gᵢ_. The rest of the predictions that are not in the resulting array are considered negative labeled predictions without a gt assignment.

# Dynamic k Estimation

_k_ is the supply of each gt object and there are two ways to calculate it. The naive way of calculating _k_ is making it a constant across all gt objects. The problem with this way of assigning supply to each gt is that not all ground truths should have the same number of anchors assigned to them.

The second proposed way of calculating _k_ is by looking at each gt separately. The authors of OTA propose using Dynamic _k_ Estimation which approximates the supply of each gt. To approximate the supply for each gt, we can look at all predictions and select the top _q_ predictions according to the IoU values between each prediction and the gt. Then, we sum up the top _q_ IoU values and use that as the _k_ value for that gt.

Using this method, we estimate the supply, or number of positive labels, for each gt by looking at how accurately each prediction bounds that gt. This way, gts with predictions that are more accurate are more likely to be assigned to that gt when using the SimOTA algorithm. The authors of OTA state the intuition behind this algorithm is that “the appropriate number of positive anchors for a certain gt should be positively correlated with the number of anchors that well-regress this gt.”

Note: Although _k_ is no longer a parameter we must change, _q_ is now a parameter that must be tuned. In my code, I use 20 as the _q_ value which seemed to work fine.

Below is my code for dynamic _k_ estimation:
 
s_i = np.ones(m+1, dtype=np.int16)# The sum of all k values  
k_sum = 0# Iterate over all ground truth boxes (i = gt_i)  
for i in range(0, m):    # Get the ith truth value  
    gt = G_reg[i]    # Get the (x, y) coordinates of the intersections  
    xA = np.maximum(gt[0], P_reg[:, 0])  
    yA = np.maximum(gt[1], P_reg[:, 1])  
    xB = np.minimum(gt[0]+gt[2], P_reg[:, 0]+P_reg[:, 2])  
    yB = np.minimum(gt[1]+gt[3], P_reg[:, 1]+P_reg[:, 3])    # Get the area of the intersections  
    intersectionArea = np.maximum(0, xB - xA + 1) * np.maximum(0, yB - yA + 1)    # Compute the area of both rectangles  
    areaA = (gt[2]+1)*(gt[3]+1)  
    areaB = (P_reg[:, 2]+1)*(P_reg[:, 3]+1)    # Get the union of the rectangles  
    union = areaA + areaB - intersectionArea    # Compute the intersection over union for all anchors  
    IoU = intersectionArea/union    # Get the q top IoU values (the top q predictions)  
    # and sum them up to get the k for this gt  
    k = np.sort(IoU)[-q:].sum()    # Add the k value to the total k sum  
    k_sum += k    # Save the k value to the supplying vector  
    # as an iteger  
    s_i[i] = int(round(k))


