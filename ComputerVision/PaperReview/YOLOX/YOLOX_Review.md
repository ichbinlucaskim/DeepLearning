 <div style="text-align: right">Final update date: Oct 29, 2023.</div>
<div style="text-align: right">Written by Lucas Kim</div>

>[!warning] Before Reading
>- My undergraduate major is not Computer Science.
>- Only 'Basic' understanding of linear algebra, calculus, and probability and statistics.
>- Might be wrong, feedback is welcome.
>- Not yet capable of fully understanding all the papers cited as references. 

***For today, I would appreciate it if you could consider my goal to be grasping the big picture.***

---

# YOLOX

## Conclusion

>[!notice] YOLOX summary
>- YOLOv3 Base
>- Used anchor-free 
>- Equipped with decoupled head and SimOTA
>- Achieved the better performance
>- Won first prize in CVPR 2021

## Prerequisites

- [[Object detection]]
- [[YOLO Family]]
- [[SOTA]]
- [[Anchor-Free]]
- [[YOLO3]]
- [[Darknet53]]

## Previous Problem

❌ Anchor-free, [[end-to-end(NMS-free)]] have not been integrated into YOLO yet
❌ [[OTA]] and OT problem
❌ Coupled head

## Solution

✅ Anchor-free model
✅ [[SimOTA]]
✅ [[Decoupled head]]

## Additional research

- Backbone
Darknet53 -> [[Modified CSPNet]]

## Performance Table

![perf](https://aicurious.io/posts-data/2021-07-28-yolox/optimized-images/exp-table-opt-2048.WEBP)



---

>[!question] Reference
> English ref
> - https://medium.com/mlearning-ai/paper-review-yolox-exceeding-yolo-series-in-2021-ffc1bd94a1f3
> - https://aicurious.io/blog/2021-07-28-yolox
> - https://andlukyane.com/blog/paper-review-yolox
> - 👍 https://medium.com/@tastekinalperenn/yolox-main-idea-behind-latest-yolo-algorithm-5f8aa930c33c
> - 👍 https://medium.com/mlearning-ai/yolox-explanation-simota-for-dynamic-label-assignment-8fa5ae397f76
>
>Korean ref
> - https://cryptosalamander.tistory.com/164
> - https://house-of-e.tistory.com/entry/8-YOLOX-Exceeding-YOLO-Series-in-2021-2021
> - https://cobslab.tistory.com/13



