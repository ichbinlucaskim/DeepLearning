 <div style="text-align: right">Update date: Sep 25, 2023.</div>
<div style="text-align: right">Written by Lucas KIM</div>

>[!warning] Before Reading
>- My undergraduate major is not Computer Science.
>- Only 'Basic' understanding of linear algebra, calculus, and probability and statistics.
>- No experience presenting papers in the field of computer vision.
>- Might be wrong, feedback is welcome.
>- Not yet capable of fully understanding all the papers cited as references. 

***For today, I would appreciate it if you could consider my goal to be grasping the big picture.***

---
# PV-RCNN
## Abstract

- 3D object detection from [[Point clouds]]
- Integrates both 3D voxel CNN and PointNet-based set abstraction
- PV-RCNN surpasses state-of-art 3D detection methods by using only point clouds.

>[!info] Code? 
> Please check [Official](https://github.com/open-mmlab/OpenPCDet) code. KITTI and Waymo [Data](https://github.com/open-mmlab/OpenPCDet/tree/master/data)
## Conclusion

- A novel method for ***accurate 3D Object Detection*** from point clouds
- Integrates both the multi-scale 3D [[Voxel]] CNN features and the PointNet-based features
- Significantly improve the 3D Object Detection performance

---
## 1. Intro

Most existing 3D detection methods could be classified into two categories (in terms of point cloud representations.)
- [[Grid-based Methods]]
	- ✅ Computationally efficient 
	- ❌ The inevitable information loss degrades the fine-grained localization accuracy
- [[Point-based Methods]]
	- ✅  could easily achieve larger receptive field
	- ❌ higher computation cost

$\Longrightarrow$ Thus, Integrate these two things (PV = Point-Voxel) 

![PV-RCNN](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/1-Figure1-1.png)

- 1. Voxel-to-keypoint scene encoding step
	- Encodes multi-scale voxel features of the whole scene to a small set of keypoints by the voxel set abstraction layer
- 2. Keypoint-to-grid feature abstraction step
	- Point-to-grid [[RoI feature abstraction]], which effectively aggregates the scene keypoint feature to RoI grids 

## 2. Related work

-  3D Object Detection with Grid-based Methods

-  3D Object Detection with Point-based Methods

-  Representation Learning on Point Clouds

## 3. PV-RCNN for Point Cloud Object Detection

![Overall architecture of PV-RCNN](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/4-Figure2-1.png)

### 3.1 3D Voxel CNN for Efficient Feature Encoding and Proposal Generation

- 3D voxel CNN
	- The input point *P* are first divided into small voxel with spatial resolution of [[L x W x H]]
	- The features of the non-empty voxels are directly calculated as the mean of point-wise features of all inside points.
	- Commonly used: [[3D coordinates]], [[Reflectance intensities]]
	- The network utilizes a series of 3 x 3 x 3 3D sparse convolution to gradually convert the point clouds into features volume with 1x, 2x, 4x, 8x

- 3D proposal generation
	- By converting the encoded 8x downsampled 3D feature volumes into [[2D bird-view feature maps]], high-quality 3D proposals are generated following the [[Anchor-Based approaches]].
---
- Discussion
	- Pooling RoI specific features from the resulting 3D feature volumes or 2D maps has major limitations. *1. Low spatial resolution, 2. Waste much computation and memory* 
	- The set abstraction has shown the strong capability of encoding feature points from a neighborhood of an arbitrary size.

- So? 🤔
	- Propose a *two step approach* to first encode voxels at different neural layers of the entire scene into a small number of keypoints and then aggregate keypoint features to RoI grids for box proposal refinement.


### 3.2 Voxel-to-keypoint Scence Encoding via Voxel Set Abstraction

*First, aggregates the voxel at the multiple neural layers representing the entire scene into a small number of keypoints.*

- Keypoints Sampling
	- Adopt [[FPS]] algorithm to sample a small number of $n$ keypoints $K = {p_1, ... , p_n}$ from the point clouds **P**
	- $n = 2,048$ for the KITTI dataset and $n = 4,096$ for the Waymo dataset

- Voxel Set Abstraction Module
	- To encode the multi-scale semantic features from the 3D CNN feature volumes to the keypoints
	- The surrounding points of keypoints are now regular voxels with multi-scale semantic features encoded by the 3D voxel CNN form the multiple levels, instead of the neighboring raw points with features learned from [PointNet](https://past-fibula-233.notion.site/Paper-Review-PointNet-a4437f2024f745e2a30070b3ff807c10)

---
*Notation*

- $N_k$ is the number of non-empty voxels in the $k$-th level

$$\mathcal{F}^{(l_k)} = \{f_1^{(l_k)}, ... , f_{N_k}^{(l_k)}\}$$
- As the set of voxel-wise feature vectors in the $k$-th level of 3D Voxel CNN

$$\mathcal{V}^{(l_k)} = \{v_1^{(l_k)}, ... , v_{N_k}^{(l_k)}\}$$
- As their 3D coordinates calculated by the voxel indices and actual voxel size of the $k$-th level

$$
   S_i^{(l_k)} =     \left\{ \begin{array}{c|l}
          
         & \Vert v_j^{(l_k)} - p_i \Vert^2 < r_k \\ [f_j^{(l_k)};v_j^{(l_k)} - p_i]^T & \forall v_j^{(l_k)} \in \mathcal{V}^{(l_k)} \\ & \forall f_j^{(l_k)} \in \mathcal{F}{(l_k)} 
                \end{array} \right \}
$$

![Explanation](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FOzyxQ%2FbtqWVRFYpO0%2Fhk5gCGU06tzeso3KSHDarK%2Fimg.png)

>[!info] Additional explanation
>Looking at the equation on the right, 
>- '$l_k$' represents the index of the feature volume 
>- '$f$' is a voxel-wise feature vector 
>- '$v-p$' denotes the relative coordinates of a voxel with respect to a keypoint. 
> 
>These two vectors are concatenated to form a set called '$S$'. The term '$r_k$' in the equation on the right signifies a radius determined for each layer k. 
>
>By setting this radius differently for each layer, we can consider that our set abstraction operation possesses a flexible receptive field. In our implementation process, we have assigned two radii per layer.

$$
f_i^{(pv_k)} = 
max\{G(\mathcal{M}(S_i^{(l_k)}))\}
$$
- $\mathcal{M}(\cdot)$ = randomly sampling at most $T_k$ voxels from the neighboring set $S_i^{(l_k)}$ **for saving computation.**
- $G(\cdot)$ = a multi-layer perceptron network to encode the voxel-wise features and relative location.

>[!info] Additional explanation
>The set is transformed into a feature vector for the keypoint through a PointNet block.
>
>Looking at the PointNet block, '$\mathcal{M}$' denotes maximum T random samplings. '$G$' represents a multilayer-perceptron layer, and 'max' is a max pooling layer.
>
>Through this process, we obtain the feature vector from each feature volume for each keypoint.

$$f_i^{(pv)} = [f_i^{(pv1)}, f_i^{(pv2)}, f_i^{(pv3)}, f_i^{(pv4)}]$$
$$f_i^{(p)} = [f_i^{(pv)}, f_i^{(raw)}, f_i^{(bev)}]$$
- for $i$ = $1, ..., n,$

>[!info] Additional explanation
>Gather the key point features calculated for each layer, and combine these with the set abstraction of raw data and the key point features obtained from a bird's eye view to form the final key point feature.
>
>The raw data was added to recover any quantization loss that may occur during voxelization, and the bird's eye view was incorporated to gain a wider receptive field along the z-axis.

$$\tilde{f}_i^{(p)} = \mathcal{A}({f}_i^{(p)})\cdot {f}_i^{(p)}$$
- $\mathcal{A}$ is a three-layer [[MLP Network]] with a sigmoid function to predict foreground confidence between $[0,1]$
- PKW module is trained by [[Focal loss]] for handling the unbalanced number of foreground/ background points in the training set.

![PKW](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fs3c8z%2FbtqWRathjzV%2FZYaHgZPAhjyCFCh2nBO8Pk%2Fimg.png)

>[!info] Additional explanation
>This module is an implementation of the idea that foreground keypoints should contribute more than background keypoints during the refinement process.
>
>We added a Point Cloud Segmentation network to calculate the confidence of whether a given point is in the [[Foreground or Background]], which is then given as a weight.
>
>The points were trained as foreground if they were inside the ground truth 3D box and as background if they were outside, without separate segmentation labels.
>
>In actual operation, we multiplied the learned network's foreground confidence by the keypoint feature to ensure that keypoints in the foreground have a greater impact on refinement.
>
>During training, we addressed issues of unbalanced distribution in our training set using focal loss.

---
### 3.3 Keypoint-to-grid RoI Feature Abstraction for Proposal Refinement

![ROI](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FXhgL3%2FbtqWXayfR3A%2FVr2NbHJUAkEVHXH1jeQW8K%2Fimg.png)

---
- ROI-Grid Pooling via Set Abtraction

![RoiGrid](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fsu2A4%2FbtqWUMylPaY%2Fgo131Qf2XMQ1X2nMQqWkUK%2Fimg.png)

>[!info] Additional explanation
>The red dots are grid points, the yellow dots are key points, and the small peach-like dots are raw points. From each grid point, a key point set is formed through various distances (receptive fields). 
>
>From this collected key point feature set, T samples are taken. A grid point feature is generated through a multilayer-perceptron layer and a max pooling layer.

- 3D Proposal Refinement and Confidence Prediction
	- The grid point features of each 3D proposal obtained earlier are processed through a 2-layer MLP to create an RoI feature vector with a dimension of 256 features.
	- Subsequently, through a network composed of two branches, we calculate confidence and box refinement.

![Confi](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbnU8ZJ%2FbtqWX54xMRB%2FC9v45tBezg9JEkbjqwpfP1%2Fimg.png)

---
*Notation*
$$y_k = min(1, max(0, 2IoU_k - 0.5)$$
>[!info] Additional explanation
>The first equation is used to calculate the confidence of each candidate RoI during training through [[IoU]], not simply having a confidence of 0 or 1. 
>
>This allows for a more nuanced learning process where better candidates are predicted to have a confidence closer to 1, and worse candidates closer to 0. 
>
>This can result in a network that can better judge which box proposal is superior during testing, compared to a conventional binary classification into 0 or 1.

$$L_{iou} = -y_k\log(\tilde{y_k})-(1-y_k)\log(1-\tilde{y_k})$$
>[!info] Additional explanation
>The second equation is the loss function for the confidence branch, which minimizes cross-entropy loss.
>
>In the box refinement branch, location residuals (errors) such as center, size, and orientation are predicted and optimized through a smooth L1 loss function.

### 3.4 Training losses

$$\mathcal{L}_{total} = L_{rpn} + L_{seg} + L_{renn}$$
>[!tip] The overall training loss are the sum of these three details 

$$L_{rpn} = L_{cls} +\beta\sum_{r \in {\{x,y,z,l,h,w,\theta\}}}+ \mathcal{L}_{smooth-L1}(\widehat{\Delta r^a},\Delta r^a )$$
$$L_{renn} = L_{iou} +\sum_{r \in {\{x,y,z,l,h,w,\theta\}}} + \mathcal{L}_{smooth-L1}(\widehat{\Delta r^a},\Delta r^a )$$
>[!info] Additional explanation
> The region proposal loss $L_{rpn}$ is the loss from the two branches in the Region Proposal Network (RPN).
> 
>$L_{cls}$ is the anchor classification loss, and $smooth-L_1 loss$ refers to the box regression branch's loss.
>
>$L_{seg}$ is the segmentation network's loss from the PKW module we examined earlier.
>
>The proposal refinement lo4ss $L_{rcnn} represents the losses from both confidence branch and box refinement branch that we've discussed previously

## 4. Experiments

- Training Detail
	- For KITTI **8 GTX 1080 Ti GPUs**, for Waymo **32 GTX 1080 Ti GPUs** 🫢

![Results](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/7-Table1-1.png)

![Resultss](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/8-Table5-1.png)

![Result2](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/7-Table2-1.png)

![Result4](https://d3i71xaburhd42.cloudfront.net/199123c3a93521a397ee4f2ae8c852b0b252f5ba/8-Table6-1.png)

---

>[!question] Reference
>- https://hblog.tistory.com/7
>- https://jaehoon-daddy.tistory.com/57
>- https://velog.io/@treeboy2762/Week-6-PV-RCNN





