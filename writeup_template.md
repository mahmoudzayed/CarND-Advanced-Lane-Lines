## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./undistort_output.png "Undistorted"
[image2]: ./undistort_image.png "Road Transformed"
[image3]: ./binary_combo_example.png "Binary Example"
[image4]: ./warped_straight_lines.png "Warp Example"
[image5]: ./color_fit_lines.png "Fit Visual"
[image6]: ./example_output.jpg "Output"
[video1]: ./output_images/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "main.ipynb" under method called Calibrate_camera.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used the S channel in HLS color space and V channel in HSV color space and created two binary image with selected thresholds. I combined tem both by an & operation so it take the best in the two images. 
I used a combination of color thresholds to generate a binary image (thresholding steps 4th cell at lines 13 through 20 in `main.ipynb`) and helper Function at 2nd cell. Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:


This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 1000, 650     | 1000, 700     | 
| 300, 650      | 200, 700      |
| 578, 460      | 200, 0        |
| 704, 460      | 1000, 0       |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
I derived the polynomial using the following steps: 
 
1- divide images into right and left sides
2- create boxe at each side of the image and for a certain portion
3- take the histogram for each side of the portion and locate the box at the highest value
4- take the derived center(points) of the boxes and create a polynomial for each box
5- take all the points and create clean polynomial that fit the line.

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated the curvature as following:
1- define conversions in x and y from pixels space to meters
2- fit the points into a polynomial with converting it to meters by multiplying with predefined conversions.
3- we choose the maximum y-value, corresponding to the bottom of the image
4- we calculate the R_curve (radius of curvature)

I calculate the distance from the center as following:
1- define conversions in x and y from pixels space to meters
2- solved the polynomial created in curvature step with max y-value
3- assume the center of the image as the expected center of the car
4- calculate the center of the road and subtracte it with the center of the car

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the second code cell of the IPython notebook located in "main.ipynb" in the function `measure_curvature_real()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
The problem was mainly in :
	1- tuning the threshhold for each filters.
	2- find the best area to wrap to get a perfect lines.
	3- calculating the curvature.
we can improve :
	1- increase the variety and combination of gradients and color filters
	2- wrap the image with better calculated areas for a better results in curvature angle.
	3- better organization of the pipline.
	4- find a better way to compensate for camera moving