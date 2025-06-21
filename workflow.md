# Calibration Algorithm Workflow
## 1. Target-Based Extrinsic Calibration: Extracting Circle Centers Using Point Cloud
### 1.1 Distance Filtering
Filter the point cloud range in the X, Y, and Z directions to remove non-target points outside the FoV.

<p align="center">
  <img src="./pics/workflow/filter.png" width="50%">
</p>

### 1.2 Plane Segmentation
Use the RANSAC algorithm to extract planes and retain the largest plane (with the most inliers).

<p align="center">
  <img src="./pics/workflow/plane.png" width="50%">
</p>

### 1.3 Plane Alignment to 2D
Rotate the plane point cloud to align with the z=0 plane, forming a 2D point cloud for subsequent(随后的) boundary point detection and circle fitting.



<p align="center">
  <img src="./pics/workflow/aligned.png" width="50%">
</p>

### 1.4 Boundary Point Detection
Detect boundary points in the 2D point cloud by calculating the polar coordinate angles of neighboring points within a 0.03m radius relative to the center point. Sort the angles in ascending or descending order, compute the differences between adjacent angles, and identify the largest angular gap. If the largest gap exceeds π/4, it is considered a boundary point.

通过计算相对于中心点 0.03m 半径内相邻点的极坐标角，检测 2D 点云中的边界点。按升序或降序对角度进行排序，计算相邻角度之间的差异，并确定最大的角度间隙。如果最大间隙超过 π/4，则将其视为边界点

源码使用`pcl::BoundaryEstimation`计算点云边界

<p align="center">
  <img src="./pics/workflow/edge.png" width="50%">
</p>

### 1.5 Clustering and Circle Fitting
Perform Euclidean clustering on the boundary points and attempt 2D circle fitting for each cluster. Filter qualified circles based on fitting error and restore the circle center coordinates from the z=0 plane to the original coordinate system.

在边界点上执行欧几里得聚类，并尝试对每个聚类进行 2D 圆拟合。根据拟合误差过滤合格的圆，并将圆心坐标从 z=0 平面恢复到原始坐标系

`kdtree + pcl::EuclideanClusterExtraction`

2D圆 `pcl::SACSegmentation` 拟合

<p align="center">
  <img src="./pics/workflow/cluster.png" width="50%">
</p>

### 1.6 Extraction Performance of Mechanical LiDAR
<p align="center">
  <img src="./pics/workflow/ouster_cluster.png" width="50%">
</p>

## 2. Target-Based Extrinsic Calibration: Extracting Circle Centers Using ArUco Marker

<p align="center">
  <img src="./pics/workflow/qr_detect.jpg" width="50%">
</p>