Ground truth is a term used in machine learning to refer to the actual data or values that you're trying to predict. In object detection, this usually includes information about the location and size of each object (bounding box coordinates), as well as its class label.

So, "$g_i$" would include:

- The bounding box coordinates for the i-th object (usually represented as x_min, y_min, x_max, y_max or center_x, center_y, width, height).
- The class label for the i-th object (e.g., 'dog', 'cat', etc.).

$L^{cls}_{ij}$ and $L^{reg}_{ij}$ are then calculated between these ground truth values and your model's predictions ($p_j$). The classification loss measures how well your model predicts the correct class for each object, while regression loss measures how accurately it predicts their bounding boxes.