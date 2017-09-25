### Advanced Lane Finding Project
---

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

[image1]: ./output_images/1_calibration.png "Calibration"
[image2]: ./output_images/2_distortion_corr.png "Distortion Correction"
[image3]: ./output_images/3_threshold_binary.png "Threshold Binary"
[image4]: ./output_images/4_perspective.png "Prespective"
[image5]: ./output_images/5_histogram.png "Histogram and Fit Binary"
[image6]: ./output_images/6_polynomial_fit.png "Polynomial Fit"
[image7]: ./output_images/7_final.png "Final Result"
[video1]: ./pipeline_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "carnd-advanced-lane-lines.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The code for this step is contained in code cells 6 and 7 of the IPython notebook located in "carnd-advanced-lane-lines.ipynb".

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

The image is corrected by calling `undistort()` function which corrects the image by applying the `cv2.undistort()` method with the previously calculated camera matrix, <code>mtx</code>, and the distortion coefficients, <code>dist</code>, retrieved from our <code>calibrate_camera()</code> function.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this step is contained in code cell 8 of the IPython notebook located in `carnd-advanced-lane-lines.ipynb`.

I used a combination of color and gradient thresholds to generate a binary image.  Here's an example of my output for this step. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for this step is contained in code cells 10 and 11 of the IPython notebook located in `carnd-advanced-lane-lines.ipynb`.

The code for my perspective transform includes a function called `warp()`, which appears in code cell 10 in the file `carnd-advanced-lane-lines.ipynb`.  The `warp()` function takes as inputs an image (`img`),  tuple for the S channel threshold (`s_thresh`), tuple for the SobelX Gradient threshold (`sx_thresh`) . I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
        [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
        [((img_size[0] / 6) + 30), img_size[1]],
        [(img_size[0] * 5 / 6), img_size[1]],
        [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
        [[(img_size[0] / 4), 0],
        [(img_size[0] / 4), img_size[1]],
        [(img_size[0] * 3 / 4), img_size[1]],
        [(img_size[0] * 3 / 4), 0]])
```

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for this step is contained in code cells 12, 13, 14 and 15 of the IPython notebook located in `carnd-advanced-lane-lines.ipynb`.

Using the search window method declared in `search_lanes()` (code cell 12) we found the lanes and then proceeded to fit my lane lines with a 2nd order polynomial using the `lane_detect()` (code cell 14).

![alt text][image5]
![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this step is contained in code cells 16 and 18 of the IPython notebook located in `carnd-advanced-lane-lines.ipynb`.

Using the methods `curvature_lanes()` and `center_pos()`, I calculated the curvature of the lane and the position of the vehicle.

For the curvature,  our equation for radius of curvature becomes:
$$R_{curve} = \frac{(1+(2Ay+B)^2)^\frac{3}{2}}{|2A|}$$

For the center, assuming that the center of the image is the center of the car, I calculated the center offset of the car in relation to the lane:
$$O_{car} = car_{center} - lane_{center}$$

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this step is contained in code cell 20 of the IPython notebook located in `carnd-advanced-lane-lines.ipynb`, using the method `generate_lane_overlay()`

Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./pipeline_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

##### Implementation

Using the Perspective Transform and a calibrated camera, I was able to do a "birds-eye" view of the lane ahead. Once with the bird's eye view image, by converting the image to HLS color space and isolating the S and L channels, a Thresholded Binary Image was created to filter out any pixels that don't belong to the lane lines and to compensate for shadows and changes of colors of the lane lines. 

The thresholded binary image was then processed through a few functions to detect the lanes and its curvature. To detect the lanes, I generated a histogram using half the image to estimate the start of the lanes, then using the "sliding window" approach, the rest of the lane was found. Then we fitted a polynomial to achieve a better fit. Using a few math equations, the curvature of the lane and the center of the vehicle in respect to the lane were acquired.

Finally, the lane was drawn on top of the original frame. This pipeline was used to process a video to show how the pipeline worked on a real case scenario.

##### Issues

Most issues were in regards to proper detection of lane lines during transitions of different pavements and when shadows affected the lanes. Most of my time was spent making sure the lane lines were properly detected even when shadows or changes of color affected the image. After trying many combinations of the HLS channels and thresholds, I found that by using both channels S and L in combination with the `cv.Sobel()` of the L-Channel, a good binary image was obtained, even when under the presence of shadows, etc...

##### Where can the pipeline fail?

 Pipeline could fail when another car/vehicle is in close proximity on the same lane, in front of our vehicle. The current lane algorithm could hypothetically fail in this situation. Another potential issue could be when running our pipeline with images or video acquired during the night. Our pipeline could face problems here.

##### Possible Solutions

In regards to the car/vehicle in close proximity issue, one possible solution could be to use deep learning to identify the vehicle in the image and its location, then remove it from the image before performing our pipeline.

For lane detection during the night, different thresholds for the binary image creation might be used that perform better.
