
![Dn](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*MIDM2bOniYzAU6SOIzUnIQ.png)

![Dn2](https://miro.medium.com/v2/resize:fit:1216/format:webp/1*Qrmw0J9r0JL8-96KzfXzHQ.png)

**What is Darknet53?**

YOLOv3 includes improvements over previous versions in terms of speed, precision, class features. YOLOv2 was using Darknet-19 as its backbone feature extractor, while YOLOv3 now uses Darknet-53. Here, we also answer the question of what Darknet-53 is. A Convolutional Neural Network that acts as a feature extractor backbone within the darknet yolo architecture. What distinguishes Darknet-53 from Darknet-19 used by YOLOv2 is that it contains 53 convolutional layers. In addition to this powerful structure, it has a much higher speed than other backbones.

**In addition, with YOLOv3, it has a higher sensitivity for smaller objects than previous versions. However, YOLOv3 allows us to assign multiple classes to an object. In previous versions, a single class assignment was made for each object with the Softmax activation function. Since it has more class sensitivity, it can distinguish between classes better. YOLOv3 can distinguish between a human and a male more easily.** 

At the same time, since it uses Darknet-53, which contains more convolutional layers than YOLOv2, it can learn more complex objects and works with higher accuracy. Considering all this, we understand why YOLOv3 is still used in many places, but we can also talk about a disadvantage. It needs to be trained with large datasets.

https://paperswithcode.com/method/darknet-53

https://kr.mathworks.com/help/deeplearning/ref/darknet53.html

