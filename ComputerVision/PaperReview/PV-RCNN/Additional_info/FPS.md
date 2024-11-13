Furthest Point Sampling (FPS) is a technique used in point cloud processing, and it is mentioned in the PV-RCNN paper. PV-RCNN stands for Point-Voxel Feature Set Abstraction for 3D Object Detection from Point Clouds, which is a model proposed for 3D object detection from point clouds.

![FPS](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbmesKK%2FbtrSAyEGooi%2FfhHKkRKBlNr2O8Za50PYfk%2Fimg.png)

In the context of point cloud data, **Furthest Point Sampling (FPS) is an algorithm used to reduce the number of points in a dense point cloud**. It works by iteratively selecting the point that is furthest away from the already selected points. This method helps to maintain the overall structure and features of the original data while significantly reducing its complexity.

Here's how FPS works:

1. Start by randomly selecting a point from your dataset.
2. Calculate the Euclidean distance between this initial point and all other points in your dataset.
3. Select as your next point the one that has maximum distance to your first chosen one.
4. Repeat steps 2 and 3 until you've selected as many points as you want for your simplified representation.

The PV-RCNN paper uses this method to subsample raw LiDAR points before further processing them into voxels (small cubes) for efficient computation while maintaining important spatial information about objects in three-dimensional space.

This approach allows them to handle large amounts of input data more efficiently, reducing computational cost without sacrificing too much detail or accuracy in their final object detection results.