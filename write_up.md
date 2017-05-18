# Advanced Lane detection

## The goal of this project is to build a lane detection algorithm that can be used in self-driving cars.
---

## To build this project, I have used the following steps:

### 1. Calibrate the Camera to get its interinsic parameters and  distortion coefficients.
### 2. Using the calibration parameters to undistort video images.
### 3. Making a binary image as a cmobination between image gradient by x and the s channel in HLS color space.
### 4. Bird eye prespective transform to the video images.
### 5. Determine the hot spots of the pixels and fit a second order polynomial to get the lane lines.
### 6. Determine the radius of the road carvature.
### 7. Calculate  the distance from the center of the lane
---

[//]: # (Image References)

[image1]: ./test_images/1.png 			"calibration"
[image2]: ./test_images/sobel_x.png 	"Grayscaling"
[image3]: ./test_images/s_image.png	 	"s_channel" 
[image4]: ./test_images/binary_image.png 	"binary_image"
[image5]: ./test_images/bird_eye.png 	"Bird eye image"
[image6]: ./test_images/poly_fit.png 	" Poly fit img"
[image7]: ./test_images/final.png 		"final imag"
[image8]: ./camera_cal/calibration1.jpg			"cal imag"
[image9]: ./camera_cal/calibration2.jpg 		"cal imag"
[image10]: ./camera_cal/calibration3.jpg 		"cal imag"



### 1. The first step in any camera-based measurement is to calibrate a camera to determine its intrinsic parameters (focal length and principle point coordinates) which are called camera matrix and to determine the radial and tangential coefficient. This can be done using a chess board with known numbers of corners in it x and y direstion. In this project, I used a chess board with 9 corners along x axis and 6 corners along y axis. Using OpenCV library and with more than 8 images of the chess board from different positions we can calibrate our camera.

![alt text][image8]
![alt text][image9]
![alt text][image10]	

* After Calibration, we will get the internsic parameters and the distortion coefficients. 

---
### 2. I used the parameters that I have got at the first step to undistort the video images. here an example of a chess board before calibration (on left) and after calibration (on right).
![alt text][image1]

---
### 3. The next step is to determine our lines which tend to be vertical so, instead of using Canny edge detection we will use one part of it, Sobel gradient by x. So, first we convert our image to a gray scale one then we will take the gradient by x and convert this gradient imag to a binary one using a proper threshold as in the next picture.
![alt text][image2]
### The problem of using this approach only is that in shadow areas the gradient will be close to zeros, also if we have yellow lane lines we will not be able to differ them from the road in gray scale images. So, HLS color space will be used. To be more specific, the S channel in HLS color space will be used because it is robust against chaning in the brightness so, it will give same result in sunny and shadow areas and the yellow line will be visible. An example of the output of S channel is in the next picture.
![alt text][image3]
### Using a combination of the gradient image and the S channel image will be a good idea. The next picture shows a combination of these two images.
![alt text][image4]

---
### 4. It is good that we have now an image which is robust to changing in the brightness but we still have a big problem. Prospective projection, is the process in which we map a 3D world to a 2D world. This mapping is exactly what happens when we use a camera. One major problem of the projection is that the parrallel lines are seemed to intersect in what is called a vanishing point if the camera plane is not parrallel to the line planes. And here in our case, the camera plane is not parrallel to the plane of the lane lines. That is why we see these parrallel lane lines tend to intersect at some point (vanishing point). To overcome this problem a bird eye prospect should be use. OpenCV library has two functions for this prospective transform and its inverse. By Using these functions, we can transform from on prospect to another. An example of the image that we get after bird eye transform is presented below
![alt text][image5]   

---
### 5. Getting a birg eye prospect allow us to see that the "hot" pixels are distributed around two main lines, each one of these lines can be represented as a second order polynomial. So, here there are two subporblems, the first is to determine where exactly the line is centered and then find the coefficients of the polynomial.
#### 5.1. To find the where the lines are centerd, I got the histogram of the bottom half of the image. From the histogram, I can see that I have two peaks, my two lines. I find the position of these two peaks to be my first points. Then I made a scan on the image from bottom to top using a window with a proper size and every time I calculates my two peaks.
#### 5.2. Doing the first step allow us to get the hot pixels within a specific window around the true place of the lines and supressing other ourliers that may exist due to lighting or road conditions. Using these hot pixels we can try to fit a polynomial using least mean square criterion. Fortunately, numpy library in python has a function called (polyfit) can do this estimatation insetead of us. Here is a picture of the image after estimating the two lane lines.
![alt text][image6]

---
### 6. The radius of the road curvature can be calculated using the polynomial coefficients. 

---
### 7. getting two lane lines allows us to get the offset of the camera from the center of the lane. To do that we first get the lane width by getting the difference between the first (from the bottom) right point and the first left point. Then, we get the distance from the right and the left borders of the image to the lane lines by subtracting the lane width from the image width and dividing by 2. This distance will be the proper distance if that car is located at the center of the lane. To get  the offset can we should subtract the the value of the first right point and the proper distance of center-located car from the image width. If the offset is positive it means that the car is shifted toward the right direction, if it is negative it means that the car is shifted to the left direction. 

### An image after determining, lanel lines, the curavter and the distance from the center can be viewed as follows:
![alt text][image7] 

---
### Further discussion
I think this work is good in ideal conditions. when we have good road, not bumpy one, when the lane lines are clear. One challenge is what will happen when one of these lane lines is erased due to bad weather or bad infrastracture. Also, with bumpy road it will be difficult to determine the two lines. I think it needs a try to improve this algorithm to be more robust against bumpy road and it is worth to think about the situations when  one or both of the lane lines disappear.

---
For the code, please check [here](Advanced-Lane-Detection.ipynb) and for a video recording, please check [here](https://youtu.be/J1MjOaXvo-E).



