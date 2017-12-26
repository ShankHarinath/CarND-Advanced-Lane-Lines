# **Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/writeup.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![CameraCalibration](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/CameraClibration.png?raw=true)

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![Undistorted](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/Undistorted.png?raw=true)

#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `_perspective_transform()` in `Pipeline` class in the file `CarND Advanced Lane Lines.ipynb`.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 240, 719      | 300, 719        | 
| 579, 450      | 300, 0      |
| 712, 450     | 900, 0      |
| 1165, 719      | 900, 719        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![Prespective Transform](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/PerpectiveTransform.png?raw=true)

#### 3. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in the method `_color_gradient_threshold()` in `CarND Advanced Lane Lines.ipynb`).  I combined Sobel absolute, Sobel magnitude, Sobel direction and HLS color threshold on `S` channel to get generate the image binary. Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![ColorSobel](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/ColorAndSobel.png?raw=true)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

With numpy's polyfit(), I fit a polynomial equation to roght and left lane. As shown in the method `locate_lanes()` under class `Lane` in `CarND Advanced Lane Lines.ipynb`.

![FitLanes](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/FitLanes.png?raw=true)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in `measure_curvature()` method under `Lane` class in `CarND Advanced Lane Lines.ipynb`. It has been displayed on the right top of with each of the output image and the output video.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `draw_lanes()` method under `Lane` class in my code in `CarND Advanced Lane Lines.ipynb`.  Here is an example of my result on a test image:

![DrawLines](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/output_images/DrawLines.png?raw=true)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://github.com/ShankHarinath/CarND-Advanced-Lane-Lines/blob/master/project_video_result.mp4?raw=true)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The current pipeline doesn't do well for the challenge videos. As per my understanding the reason is the Sobel and Color thresholds needs to be optimized more in order to detect the lanes and not anything else on the road. Some work is needed in detecting the lanes by its color (yello & white) is different color schemes. 

Also I believe to make it more robust, we can use a **Deep learning model**. This will yeild a way better result than the traditional CV methods. CV methods required loads of fine tuning for each type of road. **We can create a dataset from the scrath by drawing a polynomial on the lanes, either on the actual image or the warped image**. Then train a neural network to predict the polynomial coefficients.