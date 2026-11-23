# 3D Structure from Motion

![cover](assets/cover.png)

Incremental SfM on the [Middlebury TempleRing](https://vision.middlebury.edu/mview/data/) dataset (47 images) with Python and OpenCV. Camera poses are estimated with SIFT + PnP, then a dense point cloud is built with stereo matching between neighboring images.

## Pipeline

```mermaid
flowchart TD
    A[Images] --> B[Feature Detection<br/>SIFT]
    B --> C[Feature Matching<br/>KNN + Ratio Test]
    C --> D[Essential Matrix<br/>+ RANSAC]
    D --> E[Initial Camera Pose<br/>R, t]
    E --> F[Triangulation]
    F --> G[Initial 3D Map]

    G --> H[New Image]
    H --> I[3D-2D Correspondences]
    I --> J[PnP + RANSAC]
    J --> K[New Camera Pose]
    K --> L[Triangulate New Points]
    L --> M[Grow 3D Map]

    M -->|Next Image| H
    M -->|All Images Processed| N[Sparse Reconstruction]

    N --> O[Ground Truth Evaluation<br/>ATE RMSE 1.90 cm, rotation 2.03°]

    O --> Q[Stereo Rectification]
    Q --> S[Disparity Map<br/>SGBM]
    S --> T[Left-Right Consistency Check]
    T --> U[Back-projection to 3D]
    U --> V[Dense Point Cloud<br/>temple_dense.ply]
```

![pipeline](assets/pipeline.png)

## Results

All 47 cameras are registered. The camera position error is 1.9 cm (RMSE) on a ring with a radius of about 55 cm. The dense cloud has about 1.8 M points.

<p>
  <img src="assets/trajectory.png" width="48%">
  <img src="assets/turntable.gif" width="48%">
</p>

ORB features were tried first, but the trajectory drifted by more than 1 m. Switching to SIFT fixed it.

## Run

```
pip install numpy opencv-python matplotlib
jupyter notebook sfm.ipynb
```

Download the TempleRing data into a `templeRing/` folder next to the notebook.
