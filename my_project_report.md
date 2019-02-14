## Project Report

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

[image1]: ./output_images/test_camera_calibration1.jpg 
[image2]: ./output_images/test_camera_calibration2.jpg 
[image3]: ./output_images/test_camera_calibration3.jpg 
[image4]: ./output_images/test_camera_calibration4.jpg 
[image5]: ./output_images/test_camera_calibration5.jpg 
[image6]: ./output_images/test_camera_calibration6.jpg 
[image7]: ./output_images/test_camera_calibration7.jpg 
[image8]: ./output_images/test_camera_calibration8.jpg 
[image9]: ./output_images/test_camera_calibration9.jpg 
[image10]: ./output_images/test_camera_calibration10.jpg
[image11]: ./output_images/test_camera_calibration11.jpg 
[image12]: ./output_images/test_camera_calibration12.jpg 
[image13]: ./output_images/calibration1_undistorted.jpg
[image14]: ./output_images/test2_undistorted.jpg 
[image15]: ./output_images/test2_points.jpg
[image16]: ./output_images/test2_unwarped.jpg 
[image17]: ./output_images/test2_unwarped_R.jpg
[image18]: ./output_images/test2_unwarped_G.jpg
[image19]: ./output_images/test2_unwarped_B.jpg
[image20]: ./output_images/test2_unwarped_H.jpg
[image21]: ./output_images/test2_unwarped_S.jpg
[image22]: ./output_images/test2_unwarped_V.jpg
[image23]: ./output_images/test2_unwarped_H2.jpg
[image24]: ./output_images/test2_unwarped_L.jpg
[image25]: ./output_images/test2_unwarped_S2.jpg
[image26]: ./output_images/test2_unwarped_S_sobel.jpg
[image27]: ./output_images/test2_unwarped_S_sobelMag.jpg
[image28]: ./output_images/test2_unwarped_S_sobelDir.jpg
[image29]: ./output_images/test2_unwarped_S_sobelCombined.jpg
[image30]: ./output_images/test2_unwarped_S_sobelCombined_window_polyfit.jpg
[image31]: ./output_images/test2_unwarped_S_sobelCombined_prevfit_polyfit.jpg
[image32]: ./output_images/test2_drawn_lanes.jpg
[image33]: ./output_images/test2_drawn_data.jpg
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! PLEASE REFER TO "OUTPUT IMAGES" folder if the images are not visible here.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 4th and 5th cell of the IPython notebook located in "./advanced_lane_lines.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![][image1]
![][image2]
![][image3]
![][image4]
![][image5]
![][image6]
![][image7]
![][image8]
![][image9]
![][image10]
![][image11]
![][image12]
![][image13]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one in cell 9th in "./advanced_lane_lines.ipynb":
![][image14]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at cells 13 through 20 in "./advanced_lane_lines.ipynb"). Here are the examples of my images for this step.


![][image17]
![][image18]
![][image19]
![][image20]
![][image21]
![][image22]
![][image23]
![][image24]
![][image25]
![][image26]
![][image27]
![][image28]
![][image29]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp()`, which appears in cell 10 in the file "./advanced_lane_lines.ipynb".  The `unwarp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose to hardcode the source and destination points in the following manner:

```python
src = np.float32([(550,455),
                  (740,455), 
                  (280,700), 
                  (1150,700)])
dst = np.float32([(200,0),
                  (w-200,0),
                  (200,h),
                  (w-200,h)])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 550, 455      | 200, 0        | 
| 280, 700      | 200, 720      |
| 1150, 700     | 1080, 720      |
| 740, 455      | 1080, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![][image15]
![][image16]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Initially I started with the sobel magnitude and direction but the results were not great so I took threshold binaries of HLS L channel and HLS H channel combination to detect yellow and white lane lines. But yellow lane was still not accurate so I replaced it with Lab b-channel  and the yellow lane was detected.
Then I worked on polyfit as suggested in lectures in cells 21 to 26 of "./advanced_lane_lines.ipynb" and fit my lane lines with a 2nd order polynomial kinda like this:

![][image30]
![][image31]

I calculated the base of the lines from the histogram and calculated the windows indices from there on. After calculating the indices for these left and right lanes I used the polyfit() to get the 2nd order polynomial.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cells 27 through 28 in "./advanced_lane_lines.ipynb". I used the formula given 

```python
curve_radius = ((1 + (2*fit[0]*y*ym_per_pix + fit[1])**2)**1.5) / np.absolute(2*fit[0])
```

The radius was calculated simply by putting the values in the above formula. Ym per pix was used to convert pixels to meters. The center distance was calculated by assuming camera at the center and calculating the lane center then taking difference from car position and lane position.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in cells 29 through 32 in "./advanced_lane_lines.ipynb" in the function `draw_lane()`.  Here is an example of my result on a test image:

![][image32]
![][image33]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.

Due to lack of time as this is an accelerated course given by Udacity for KPIT scholarship, I just tried to implement all that was mentioned in the course content and lectures, so was not able to fiddle a lot with the sobel operator, other color spaces etc. 

The pipeline is likely to fail in bad lighting conditions and shadows, also not sure of the  very steep curve case like around a hill.

To make things more robust more images can be taken of low lighting conditions. More work can be done in polyfit to make lines more accurate.
