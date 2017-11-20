## Writeup

---

**Advanced Lane Finding Project**

The steps for this project are the following:

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
[image2_5]: ./output_images/undistort_test1.jpg "Road Transformed"
[image3]: ./output_images/binary_test1.jpg "Binary Example"
[image4]: ./output_images/wrapped_straight_lines1.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/final_test1.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points


---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

In project.ipynb I have created a function 'calibrate_camera()' to do the calibration. It takes folder name of disorted images as input and creates calibration.p pickle file containing parameters, which I have used latter.

First I have created object_point using np.mgrid and also created image_point array containing corners in distorted images. To find calibration parameters, I have used calibrateCamera() function of openCV.
![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

![alt text][image2]
![alt text][image2_5]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image.  Here's an example of my output for this step. In project.ipynb I have created a function 'binarize()' which takes undistorted image as input and returns binary image. I have converted RGB to HLS and used L channel and S channel for binarization. I have also took only x sobel for S. Final output is combination of all.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I have selected source and destination point by looking at straight test images. In project.ipynb I have created a function 'transfrom()' which takes binary image as input and return binary wrapped image and Minv, which I am using later for retransformation.

Following source and destination points I have used:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 226, 700      | 226, 710      | 
| 1079, 700     | 1079, 710     |
| 684, 450      | 1079, 100     |
| 593, 450      | 226, 100      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In project.ipynb I have created a function 'find_lanes_pixels_from_scratch()' which takes binary wrapped image as input and return point(x and y cordinate) for left and right lane.

First I buit the histogram of aggregated bottom half of the image at y axis to get the base point for lanes. After that I have devided the image in 9 windows and update the lane center based on the non-zero point in rectangular window of margin 125 pixels.
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

In project.ipynb I have created a function 'find_curve()' which takes point(x and y cordinate) for left and right lane as input and returns polyfit of degree 2 for right lane and left lane. This function also returns average curvature of road in meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I have used 'draw_lane()' function in which I am overlaying lane curves on undistorted images. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

I have made pipeline() function to do all manipulations is order. I have used this on test images and for video.
 
Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline is not very efficient as I am finding lane points of each frame seperatly and not using the lane polyfit for previous iteration. My pipeline is not capturing any sequential information. In next iteration I would like to use previous polyfit and can also do sequential smoothening.