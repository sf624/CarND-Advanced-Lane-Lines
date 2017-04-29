##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

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
[image2]: ./test_images/test5.jpg "Road Transformed"
[image3]: ./output_images/binary_combo.jpg "Binary Example"
[image4]: ./output_images/warped_binary.jpg "Warp Example"
[image5]: ./output_images/color_fit_line.png "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[video1]: ./project_result.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the IPython notebook located in "Extract distortion matrix.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

All codes below exists in "Project notebook.ipynb".

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image ( the codes are in 5th cell). Especially L and S value of HLS color space and x direction gradiant using sobel operator (kernel_size = 3) were used. Here's an example of my output for this step.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in the 2nd code cell.  The `warp()` function takes as inputs an image (`img`).  I chose to hardcode the source and destination points in the following manner:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 194, 719      | 320, 719        | 
| 595, 449      | 320, 0      |
| 685, 449     | 960, 0      |
| 1117, 719      | 960, 719        |

These points were chosen by reffering to "test_images/straight_lines1" assuming that the lane line in the image is straight and is on flat plane. Below is the result of warp of the binary image shown just before.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I used sliding window technique to detect the lane line from the warped binary image, but this method was skipped if the lane lines were detexted in previous frame. The corresponding pixels in lane lines were used to generate 2nd order polynomials that approximate the lane line. Also the result of averages throught last 5 frames. The result image is as follows:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in 9th and 13th code cells. As for curvature, the pixel cordinates in lane line were corrected to actual size and re-fitted to 2nd order polynominal and its coefficient was used to calcluate the radius of curcature. As for horizontal position in a lane, the difference between horizontal center of image and mean position of left lane and right lane at the bottom of image was calculated.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in 11-12th code cells.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_result.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In order to overcome the shadow which appears in the project video, I've used Lightness value in HLS color space for threshold and generate binary image. So this code might fail in darker situation (eg. Night driving). In this project, the fact that the color of lane lines must be either white or yellow was not utlized in the code. So using the color information might help improve this project code.
