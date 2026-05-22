# Library of Image Processing Functions and Algorithms

Modular library of image processing functions and algorithms that will be used to solve a set of practical computer vision problems.



## Project Overview
Project implements 8 different algorithms:   
    1.  Selective Object Enhancement (Color-Based Editing Tool)   
    2.  Comparative Study of Sharpening Pipelines for X-Ray Images  
    3.  Intelligent Auto Image Enhancement System  
    4.  Document Cleaning System   
    5.  Panorama Image Stitching
    6.  Transformed Object Recognition  
    7.  Depth Approximation from Two Images  
    8.  High Dynamic Range (HDR) Imaging

## Task 1: Selective Object Enhancement
Goal: Enhance a specific object in an image while applying different visual
effects to the rest of the image.

  
Pipeline:  
    1. Define HSV color range.  
    2. Create mask with color thresholding.  
    3. Apply specific enhancement based on required task. (blur, sharpen, and grayscale)  
    4. Apply the enhancements on both background and target.  

\
Example:

```python
selective_enhance(
    r"Adham's Images/red_yellow.jpg",
    target_color="red",
    target_enhancement="sharpen",
    background_enhancement="blur"
)
```

## Task 2: Comparative Study of Sharpening Pipelines for X-Ray Images
Goal: Compare between three different image enhancement strategies (pipelines) for enhancing
X-ray image.

First Pipeline:
- Sobel Edge Detection
- Smoothing (5 × 5 averaging)
- Mask Creation: (Smooth)2
- Sharpened: f + Mask
- Gamma Correction

Second Pipeline:
- Laplacian
- Smoothing (5 × 5 gaussian)
- Mask Creation: (Smooth)2
- Sharpened: f + Mask
- Logarthmic

Third Pipeline:
- Canny
- Smoothing (5 × 5 gaussian)
- Mask Creation: (Smooth)2
- Sharpened: f + Mask
- Sigmoid

Example:
```python
sobel_pipeline = SharpeningPipeline(
    edge_method="sobel",
    smooth_method="average",
    final_transform="gamma"
)

sobel_pipeline.compare(r"Adham's Images/xray.png")
```

## Task 3: Intelligent Auto Image Enhancement System
Goal: Automatic image enhancement for exposure correction and low-contrast images.  

Implementation: 
A single main method enhance() takes an image as input. The image is converted into
the LAB colour space and split into three channels; only the L channel (luminance) is
processed, while the A and B channels are preserved and merged back at the end.
The method measures the average luminance value across all pixels as a brightness indicator.
### Enhancement Cases:
- Overexposed Image (Mean L > 160)  
The image is scaled linearly across all pixels to approximately 120 luminance.  
A gamma of 1.5 is then applied to non-linearly compress bright spots and recover
highlight detail.

- Underexposed Image (Mean L < 90)  
A gamma of 0.5 is applied, which enhances darker spots more than bright ones, revealing
shadow detail.
  
- Normally Exposed Image (90 ≤ Mean L ≤ 160)  
CLAHE (Contrast Limited Adaptive Histogram Equalization) is applied. It divides the
image into separate tiles and applies histogram equalization to each tile independently,
enhancing contrast without significantly altering exposure.

Example: 
```python
enhance_v3(r"Sohayl's Images/bright.png")
```
## Task 4: Document Cleaning System
Three different techniques are implemented to clean dirty areas in documents:  
    1. Median Filtering  
    2. EDE Method (Edge Detection, Dilation & Erosion)  
    3. Adaptive Thresholding  

### Median Filtering
Median filtering is the simplest denoising technique and follows two basic steps:  
    1. Obtain the “background” of an image using Median Filtering with a kernel size of
    23 × 23.  
    2. Subtract the background from the image, leaving only the “foreground” (text and significant document details) clean of any background noise.

###  EDE Method (Edge Detection, Dilation & Erosion)
Before applying this technique, images are preprocessed to remove edge noise.  
    1. Dilation makes lines thicker by adding pixels to boundaries, “filling in” the text
    while keeping stain edges hollow.  
    2. Erosion (the reverse operation) completely removes thin lines while preserving the
    thicker text lines

### Adaptive Thresholding
Thresholding sets all pixels above a threshold to 1 (background) and the remaining pixels
to 0 (foreground). Adaptive thresholding computes a per-pixel threshold rather than a
single global value, using Gaussian Thresholding: the threshold is the weighted sum
of neighbouring pixel intensities with Gaussian-weighted windows.  
Key parameters:  
• Block Size (7): Determines the size of the pixel neighbourhood. Larger values
provide more global context; smaller values capture fine details.  
  
• C (23): A fine-tuning buffer to help filter out residual noise.  
\
Example:
```python
adaptive(r"Kiro's Images/dirty-img.png")
```
## Task 5 Panorama Image Stitching
Goal: Merge two overlapping images of the same scene into a single seamless
panoramic image.

Pipeline:
- SIFT Feature Extraction (find feature matches)
- FLANN Matching and Ratio Test (find feature matches)
- Homography Estimation and RANSAC (compute homography)
- Perspective Warping and Alpha Blending (warp and blend)
- Bounding Box Cropping (crop black borders)


Example: 
```python
panorama_pipeline()
```

## Task 6: Transformed Object Recognition
Goal: Identify an object in an image even if it has been rotated,
translated, or slightly transformed.

Pipeline:  
    1. Both images are converted to grayscale and fed to the SIFT algorithm, which
    returns a list of keypoints (distinct features) alongside a descriptor for each keypoint
    (a unique fingerprint).  
    2. If no descriptors are found in either image, SIFT returns None and the process
    stops.  
    3. Otherwise, a matcher takes each descriptor from the reference image and finds the
    two closest matches in the scene image. A match is only kept if the best match is
    clearly better than the second-best; ambiguous matches are discarded.  
    4. If at least 4 good matches are found, their coordinates are extracted from both
    images and passed to the homography estimator.  
    5. The RANSAC algorithm identifies geometrically consistent matches and outputs
    a mask labelling each match as inlier or outlier.  
    6. Outlier matches are removed during the drawing stage.  
    7. The final output shows both images side by side with green lines connecting the
    surviving verified matches.  
    8. If fewer than 4 matches are found, the mask is considered null and surviving matches
    are drawn without any geometric filtering.  

Example:
```python
if __name__ == "__main__":
    lib = VisionLibrary()
    
    img_ref = cv2.imread(r"Sohayl's Images/ref.png")
    img_scene = cv2.imread(r"Sohayl's Images/scene.png")
```

## Task 7: Depth Approximation from Two Images
Goal: estimate relative depth information from two images of the same scene
taken from slightly different viewpoints 

Pipeline:
-  Image Loading and Resizing
-  Image Alignment (Registration)
-  Preprocessing
-  Disparity Estimation
-  Normalization

Example:
```python
depth_map, heatmap = stereo_depth_pipeline(
    r"Kiro's Images\Left.jpg",
    r"Kiro's Images\Right.jpg"
)
```

## Task 8: High Dynamic Range (HDR) Imaging
Goal: Combine multiple images of the same scene captured with different
exposure levels into a single image with improved dynamic range and detail visibility.

Pipeline:
- Exposure Bracketing
- HDR Merge
- Tone Mapping (Reinhard)

Example: 
```python
cv2.imwrite("hdr_raw.jpg", hdr_raw)
cv2.imwrite("hdr_tone_mapped.jpg", hdr_tone_mapped)
```
