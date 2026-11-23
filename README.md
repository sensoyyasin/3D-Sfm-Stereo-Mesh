# 3D Structure from Motion

![cover](assets/cover.png)

Incremental SfM on the [Middlebury TempleRing](https://vision.middlebury.edu/mview/data/) dataset (47 images) with Python and OpenCV. Camera poses are estimated with SIFT + PnP, then a dense point cloud is built with stereo matching between neighboring images.

## Pipeline

```mermaid
flowchart LR
    subgraph S1[Initialization]
        direction TB
        A[Images] --> B[SIFT features]
        B --> C[Matching<br/>KNN + ratio test]
        C --> D[Essential matrix<br/>+ RANSAC]
        D --> E[Initial pose<br/>R, t]
        E --> F[Triangulation]
    end

    subgraph S2[Incremental SfM]
        direction TB
        H[New image] --> I[3D-2D matches]
        I --> J[PnP + RANSAC]
        J --> L[Triangulate new points]
        L -->|next image| H
        L --> N[Sparse reconstruction<br/>ATE 1.90 cm, rotation 2.03°]
    end

    subgraph S3[Dense reconstruction]
        direction TB
        Q[Stereo rectification] --> R[SGBM disparity]
        R --> T[Left-right check]
        T --> U[Back-projection to 3D]
        U --> V[temple_dense.ply]
    end

    S1 --> S2 --> S3
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
