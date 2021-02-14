# Advanced Lane Finding Project

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

[image1]: ./output_images/adding_corners_chess.png "Corners Added"
[image2]: ./output_images/Adding_Sliding_Windows.png "Adding Sliding Windows to detect Lines"
[image3]: ./output_images/Undistorted_Calibration_Image.png "Adding Sliding Windows to detect Lines"
[image4]: ./output_images/thresholded_gradient.png "thresholded gradient"
[image5]: ./output_images/undistorted_img.png "removing distortion"
[image6]: ./output_images/warped_img.png "warped image"
[image7]: ./output_images/warping_S_channel.png "Transforming to  S channel "
[image8]: ./output_images/Thresholded_Gradient_Full.png "Apply a thresholded gradient to the full images"
[image9]: ./output_images/hls_full.png "HLS conv to the full image"
[image10]: ./output_images/Drawing_between_lanes.png "Drawing between lanes"
[image11]: ./output_images/display_final_image.png "Display the results on the final image"
[video1]: ./project_video.mp4 "Video"
[video2]: ./result.mp4 "Final Video"


---
The code for the whole project is contained in the first code cell of the IPython notebook located in "Advanced-Lane-Detection.ipynb".  


# Here I will discuss each point of the project and how i did it : 

### Camera Calibration

#### 1.Here I will discuss how i made the calibration for the camera 



I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in all of the  test images.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection you can see an example of the corners detected in the chessboard image in the figure below. 

![alt text][image1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration Matrix and distortion coefficients using the function  `cv2.calibrateCamera()`.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image3]

### Pipeline (single images)

#### 1. Here I will  Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

![alt text][image5]

As you see that the distortion was removed correctly .
#### 2.Here I will describe how  i used color transforms, gradients to create a thresholded binary image.  And I will Provide an example of a binary image result.

First I have applied a sobel function to the images  used a combination of color and gradient thresholds to generate a binary image (thresholding steps at the function apply_mag_sobel  # Inthrough # in `Advanced-Lane-Detection.py`).  Here's an example of my output for this step.

![alt text][image8]

Seconde after investigating the results it seems that the sobel function is not usefull enough to detect all the yellow lanes in the images so i have transformed the channel of the images into a HLS transformation and taking only the S channel from the hls transformation . and when examining the results it was better than before . The code of this step is at the function (hls_conv). You can see the results of this step in the figure below.

![alt text][image9]

#### 3. Here i will describe how i have  performed a perspective transform and i will provide an example of a transformed image.

The code for my perspective transform can be found  in the  function called which is called  `prespective_transform`,   .  This  function takes as inputs an image (`img`)and then remove the distortion in the image after calling the function used to remove distortion and the i have defined src and dist points in order to make the perspective transform i have choosed the points manually in the following manner : 
```python
src = np.float32([[490, 482],[810, 482], [1250, 720],[40, 720]])
dst = np.float32([[0, 0], [1280, 0], [1250, 720],[40, 720]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 490, 482      | 0, 0        | 
| 810, 482      | 1280, 720      |
| 1250, 720     | 1250, 720      |
| 40, 720      | 40, 720        |

and you can see the results of this step in the figure below 
![alt text][image6]

#### 4. Here i will describe how i have  identified lane-line pixels and fit their positions with a polynomial :

First I have detected the lane lines using Histogram Peaks Method to detect the starting points of both left and right  lines , then i have  fit a polynomial to the lines to determine the curvature . and i  have used sliding windows method in order to draw the curvature of the lines .


You can see the results in the figure below. 
![alt text][image2]


Second I have filled between the two lanes and i have transformed the warped image back in order to better detection of the lanes.

You can find the results of this step in the figure below .

![alt text][image10]



#### 5. Here i will describe how i have  calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
after i have made the perspective transform i have calculated the radius of the curvature of both the right and left lanes by first identifing a ym_per_pix which is pixels in a meter in the y direction and xm_per_pix which is the same but in x direction and then i have made a variable called ploty which is simply a set of numbers in order to can fit the polynomial of the two lines then i calculated the radius of curvature using the calculations from 'https://www.intmath.com/applications-differentiation/8-radius-curvature.php'

and in order to determine the center of the vehicle i defined a variable called xmax which is the the maximum value of the points on the x axis and then i have divided it by 2 to get the center of the car .


#### 6. Here i will provide an example image of the  result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `Advanced-Lane_Detection.py` in the function `display()`.  Here is an example of my result on a test image:

![alt text][image11]

---

### Pipeline (video)

#### 1. Here i will provide a link to my final video output.  

Here's a  My Final result video  

![alt text][video2]


---

### Discussion

#### 1. Here i will Briefly discuss the problems / issues i have faced in my implementation of this project.  Where will my  pipeline likely fail?  What could i do to make it more robust?

the video pipeline developed  did a good  job of detecting the lane lines in the project video provided for the project, which shows a road in basically ideal conditions, with fairly distinct lane lines, and on a clear day. It also did a decent job with the challenge video, although it did lose the lane lines momentarily when there was heavy shadow over the road from an overpass.
What I have achieved from this project is that it is  easy to implemeny a software pipeline to work good for fixed  road and weather conditions, but what is challenging is implementing a software pipeline which will be able to detect lane lines under any condition like rain or dust .when testing my pipeline on the challenging video which is more hard than the project video my pipeline doesn't work well as the project video due to the changing conditions like shadow and road quality.

I think to make the pipeline more robust to handle different wheather conditions and road quality i can add more resources of several images from different conditions to tune my parameters on so it can handle the different conditions or try to use CNNs to train on different conditions and then apply it to detect lanes in different conditions.