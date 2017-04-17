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

[undist]: ./examples/undist.png "Undistorted"
[straight_lines2]: ./test_images/straight_lines2.jpg "Road Transformed(straight_line)"
[test5]: ./test_images/test5.jpg "Road Transformed(curve)"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

I have 2 Jupyter notebooks for project_video.mp4:

* [Term1-P4.ipynb](./Term1-P4.ipynb): This is code slices to describe the process of images, including undistortion, combination, warping and so on.

* [P4.ipynb](./P4.ipynb): I made all the code slices together to classes, and produced a output.mp4.

---
###Camera Calibration

####1. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #1, I read all the calibration jpgs from camera_cal directory, and calculated the image points and object points.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

####2. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #3, I undistort an image 'camera_cal/calibration1.jpg'(not used in calibration because of wrong shape).
I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![undist][undist]

###Pipeline (single images)

####1. I tested many channels and gradients on two test images, one for straight line, one for curve.
![straight_lines2][straight_lines2]![test5][test5]

####2. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #7, I output some channels and gradients for straight_line2.jpg.
From the output, you can see that both the (R + Sobel-x) and the (S + Sobel-x) held the lines clearly.

####3. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #8, I output some channels and gradients for test5.jpg(curve).
From the output, also, you can see that both the (R + Sobel-x) and the (S + Sobel-x) held the lines clearly, but (R + Sobel-x) is better.

####4. I got a conclusion, combination of R-channel and Sobel-x would be a good option.

####5. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #9, I calculated the perspective transform from straight_line1.jpg, and output them.

The code for my perspective transform includes a function called `warpMtx`, which returns the matrix not the warped image, because it will be more reasonable since not every image are at same shape. In the `warpMtx()` function, I chose the hardcode the source and destination points in the following manner:

```
src = np.float32([ [205, 719], [594, 450], [686, 450], [1105, 719] ])
dst = np.float32([ [200, 719], [200, 0], [900, 0], [900, 719] ])
```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 205, 719      | 200, 719      | 
| 594, 450      | 200, 0        |
| 686, 450      | 900, 0        |
| 1105, 719     | 900, 719      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

Please check the code #9, there are the outputs.

####6. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #14, I have a function called `find_lane`, which takes in a warped binary image, and returns the found lane.
PS: This function should consider two conditions(detected, or not), that would be solved in P4.ipynb.

I used window search to find the points in each line. Then i used numpy.polyfit to fit a polinomial.

I drew the fit lines and the lane onto the warped image, and displayed them. Please check the code output.

####7. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #14, I calculated the radius of curvature of the lane and the position of the vehicle with respect to center. And also, I print out the results. Please check the code output.

####8. In [Term1-P4.ipynb](./Term1-P4.ipynb) code #16, I used all the functions above together to draw a lane on a image. The function called `draw_lane`, which takes in a image as a input, and output a iamge with a detected lane.

Please check the code output, there is a output image with detected lane of test_images/test5.jpg.


###Finally, i put all the code together into classed defined in P4.ipynb, and produce a output.mp4.

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [project output video](./output.mp4)

---

###Discussion

####1. The project_video.mp4 is more simple, because the lines are very clear. Then I tried several days to work out the challenge_video.mp4, but still not get a good detection on every frame. 
There are many noises and sometimes the magnitude of noise is more than lines. 
I found R-channel works better in bright frames, and the Mag & Direct worked better in dark frames. So I used both to detect lane.
It worked on many frames, but failed on the start frames because there are a noisy line close to the real right line.
I need to figure that out, and maybe then i would solve the challenge video.
I will continue work on challenge video.

