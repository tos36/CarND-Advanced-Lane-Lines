## Writeup

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

[image1]: ./output_images/1_dist.png "Undistorted"
[image2]: ./output_images/2_dist_1.png "Undistortion1"
[image3]: ./output_images/2_dist_3.png "Undistortion2"
[image4]: ./output_images/3_thresh_1.png "Binary1"
[image5]: ./output_images/3_thresh_3.png "Binary2"
[image6]: ./output_images/4_warp_1.png "Warp1"
[image7]: ./output_images/4_warp_3.png "Warp2"
[image8]: ./output_images/5_search_1.png "Search1"
[image9]: ./output_images/5_search_3.png "Search2"
[image10]: ./output_images/7_result_1.png "Result1"
[image11]: ./output_images/7_result_3.png "Result2"
[image12]: ./output_images/8_result_1.png "Result with text1"
[image13]: ./output_images/8_result_3.png "Result with text2"

[video1]: ./output_images/project_video.mp4 "Video"
[video2]: ./output_images/project_video_2.mp4 "Video2"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the #1 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. 

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The code for this step is contained in the #2 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb". The function is called `dst_correction()`.

Here I applyed undistortion using the parameter calcurated in the previous section.
test images like this one:
![alt text][image2]
![alt text][image3]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this step is contained in the #3 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb". The function is called `pipeline()`.

I used a combination of 

- Red color thresholds 

- Gradient descend thresholds

to generate a binary image

I tried but didn't apply S-color thresholds, since it detects boundaries of shadow on the road.

Here's an example of my output for this step.  

![alt text][image4]
![alt text][image5]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for this step is contained in the #4 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb". The function is called `pers_tfm()`.

The function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
center = 640
off_l = 410
off_h = 57
src = np.float32([[center - off_h, 460], 
                  [center - off_l, 720], 
                  [center + off_l, 720], 
                  [center + off_h, 460]])
dst = np.float32([[300, 0], 
                  [300, 720], 
                  [980, 720], 
                  [980, 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 583, 460      | 300, 0        | 
| 230, 720      | 300, 720      |
| 1050, 720     | 980, 720      |
| 697, 460      | 980, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image6]
![alt text][image7]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for this step is contained in the #5 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb". The function is called `fit_polynomial()`.

If there's no result from a previous frame, this function call `find_lane_pixcels()` and

- Take a histogram of the bottom half of the image and find the peaks.
- Identify the nonzero pixels within each windows 
- Concatenate the arrays of indices
- Fit polinomyal curves


If curve is fitted in previous frame, this function call `search_around_poly()` and

- Identify the nonzero pixels around given polynomial curves
- Fit polinomial curve

Followings are the image of the result.

![alt text][image8]
![alt text][image9]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this step is contained in the #6 section of the IPython notebook located in "./my_code/Lane-Line-Detection.ipynb". The function is called `measure_center()` and `measure_radius`.

Here I assume the camera is placed the center of the car, and lane width is always 3.7m.
`measure_center()` difine the position of the vehicle as a difference of the center of fitted lanes and center of the image. 

The radius of the curcature is calculated from fitted polynomial.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this step is contained in the #7, 8 section of the IPython notebook located in "./my_code/Lane-Line-Det
ection.ipynb". The function is called `draw_lane()` and `measure_radius`. Here is an example of my result on a test image:

![alt text][image12]
![alt text][image13]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video.mp4)

I also impremated the piplie which search lane araound the polynomial cureve caluculated in the previous frame.

Here's a [link to the video result](./output_images/project_video_2.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

- Optimization of color transform parameters. It still detect edges of the road, shadow and will not work for night scenes.
- Processing speeds. In real-world application, this should work much faster.
- Fluctuation of radius and center position. This will be improved by applying low-pass filters (like moving average) using the information from previous frames.

