
Point-based methods for 3D object detection **directly process the raw point cloud data without transforming it into other formats like voxels or images**. This is in contrast to grid-based methods, which discretize the 3D space into a grid of voxels.

Point-based methods often use deep learning architectures that **can handle irregular and unordered point cloud data**. Here's a general idea of how these methods work:

1. **Input**: The input is a set of points from a point cloud. Each point has its own set of features such as its coordinates (x, y, z), intensity, and sometimes additional features like color or reflectance.
    
2. **Feature Extraction**: PointNet or PointNet++ are commonly used to extract features from the raw points. These networks can learn hierarchical features by applying several layers of transformations and pooling operations.
    
3. **Object Proposal Generation**: After feature extraction, region proposal networks (RPNs) can be used to propose potential bounding boxes that might contain objects.
    
4. **Box Refinement and Classification**: For each proposed box, additional network layers predict whether the box contains an object (and what type), as well as refine the box dimensions to better fit the object.

An example of such point-based method would be PointRCNN or VoteNet which are designed specifically for 3D object detection tasks **using LiDAR data in self-driving car technologies**.

One advantage of point-based methods is that they preserve all original information because they don't need voxelization or projection steps that might lose some details. However, they may also require more computational resources than grid-based methods because they need to process each individual point rather than aggregated voxels.

> [!info] References

