**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binay_image_combined.png "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/window_based.png "Fit Visual"
[image6]: ./examples/poly_based.png "Output"
[image7]: ./examples/example_output.jpg "Result"
[video1]: .output_videos/project_video.mp4 "Video1"
[video2]: .output_videos/challenge_video.mp4 "Video2"
[video3]: .output_videos/harder_challenge_video.mp4 "Video3"

### Camera Calibration

#### 1. Computation of the camera matrix and distortion coefficients.

* Find chess board corners within a chessboard of size (9, 6) by using the opencv function `cv2.findChessboardCorners()`
* Make a list of objpoints, imgpoints in such a way that objpoints are real world coordinates (x, y, Z=0) of the chess board corners and imgpoints are their image coordinates respectively. 
* Use `cv2.calibrateCamera()` function to calibrate the camera. This function returns a camera matrix (mtx) and distortion coefficients (dist). 
* Use mtx and dist parameters to undistort an input image using `cv2.undistort()` function
* The following image shows the result of before and after distortion.
![alt text][image1]

### Pipeline (single images)

#### 1. Distortion-correction of the input image.

* By using the calculated camera matrix and distortion coefficients, the input image is undistorted as below.
![alt text][image2]

#### 2. Binary image creation
* The input image is converted from RGB to HLS color space.
* Sobel filter is applied in x-direction onto the L color channel.
* The x-gradient image is further processed to compute its absolute values and normalized values.
* The S- channel is filtered using threshold values.
* Both S and L channels are fused together after thresholding to create a binary image.

![alt text][image3]

#### 3. Perspective Transformation (Birds-Eye view)
* The opencv function `cv2.getPerspectiveTransform(src, dst)` is used to transform the input image to see how it will look from the birds eye view.
* A parameter in the function `src` contains four points lying on the road as shown in the image below on the left (Four corner points of the red rectangular box drawn on the road). Another parameter `dst` contains four points where the `src` points to be transformed. 

![alt text][image4]

#### 4. Implement Sliding Windows and Fit a Polynomial

* A histogram is calculated on the lower part of the input image. The left peak in the histogram represents a pixel location of the left lane and a peak on the right side represents a pixel location of the right lane.
* With refernce to these peaks the location of both lanes are tracked in the complete image as shown below

![alt text][image5]

#### 5. Use the previous polynomial to skip the sliding window
* The window based lane search is done only for the first frame.
* From second frame onwards the lanes are tracked based on polynomial computated in the first frame.
* A new region of interest is considered which is close to the last polynomial curve with a margin of +/- 100 pixels.
* This helps to reduce computational overheads.
![alt text][image6]

#### 6. Result
* Finally, a green color region is plotted between the tracked lanes as shown in the image below.
![alt text][image7]

---

### Pipeline (video)

The result on a real data can be seen [here](https://www.youtube.com/watch?v=jf5B_ihmM-k)
* Video 1 [video1]
* Video 2 [video2]
* Video 3 [video3]

---

### Discussion

#### 1. Challenges

* The road lanes in challenging video are not successfully tracked because of the following reasons.
  * Changing lightneing conditions
  * More number of vertical edges in the images make it difficult to detect the exact position of the lanes.
