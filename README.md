# segmentation-and-pose-estimation-using-grabcut-and-pca
A focused study on foreground segmentation using GrabCut and orientation estimation using PCA, applied to dental instrument images.

## Abstract

This work presents a classical computer vision pipeline for accurate segmentation and 2D pose estimation of dental instruments from single RGB images. The proposed method avoids deep learning dependency and instead leverages GrabCut segmentation, morphological refinement, connected component analysis, contour geometry, and Principal Component Analysis (PCA) to estimate the principal axis of elongated medical tools. The final output includes a binary segmentation mask, isolated instrument region, and a geometrically interpretable orientation angle relative to a horizontal reference axis, visualized with an angular arc overlay.

# Objective

The primary objective of this system is to estimate the dominant orientation (pose) of a dental tool in 2D space, given a single image, in scenarios where:

Tools exhibit low inter-class visual variation
Metallic instruments lack strong texture features
Real-time or lightweight solutions are preferred over deep models
# Methodology Overview

The proposed pipeline consists of the following sequential stages:

1. Image Acquisition

Input images are retrieved from remote sources and decoded into OpenCV-compatible format. Standard BGR-to-RGB conversion is applied for visualization consistency.

2. Region of Interest Initialization

A manually defined bounding box is used to initialize the segmentation process. This step constrains foreground estimation and reduces background ambiguity.

Let:

ROI=(x,y,w,h)

3. Foreground Segmentation via GrabCut

We employ the GrabCut algorithm, formulated as a graph-cut optimization problem:

Pixels are modeled as Gaussian Mixture Models (GMMs)
Energy minimization separates foreground from background
Iterative refinement improves segmentation quality

This yields an initial binary mask:

M(x,y)∈{FG,BG}
4. Morphological Post-Processing

To enhance spatial consistency of segmentation:

Morphological Opening removes small noise artifacts
Morphological Closing fills internal holes
M′=(M∘K)∙K

where K is a structuring element.

5. Connected Component Analysis

To isolate the target object:

All connected regions are labeled
The largest connected component is selected:
C*=arg max|Ci|

This step ensures robustness against segmentation artifacts.

6. Contour Extraction

The boundary of the selected component is extracted using:

cv2.findContours
Contour is represented as a set of 2D points:
P={(xi,yi)}i=1 ->N
	​

7. Dimensionality Reduction via PCA

Principal Component Analysis is applied to contour points:
Σ=N1​∑(P−μ)(P−μ)T

Eigen decomposition yields:

First eigenvector → dominant direction of elongation
Mean point → geometric center

Thus:

Orientation vector:
v=(vx​,vy​)
Center:
c=(μx​,μy​)
8. Orientation Angle Estimation

The tool angle relative to horizontal axis is computed as:

θ=arctan2(vy​,vx​)

Converted to degrees and normalized to:

θ∈[0,180]
9. Geometric Visualization

Final visualization includes:

Green color for the Centroid of the object
Red color for the Principal axis (PCA direction)
Blue color for the Reference horizontal axis

Yellow color is for the Angular arc representing:

Δθ=θ−0∘

The arc is rendered using elliptical geometry for interpretability.

## Key Contributions
- Classical non-deep learning pose estimation pipeline
- Robust segmentation using GrabCut + morphological filtering
- Stable orientation estimation via PCA on contour space
- Geometric interpretability through explicit angle visualization
- Lightweight solution suitable for embedded or robotic systems
## Scientific Insight

Unlike learning-based approaches, this method assumes:

Shape information is sufficient for pose estimation
Tool geometry is approximately elongated and rigid
Principal axis ≈ physical orientation axis

This makes PCA a valid estimator of orientation in constrained medical instruments.


## Conclusion

This study demonstrates that classical geometric vision methods remain effective for structured medical objects, achieving interpretable and computationally efficient pose estimation without reliance on data-driven learning models.
