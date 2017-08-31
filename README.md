## UDACITY Self Driving Car Nano Degree Term 1 Project 4---

**Advanced Lane Finding Project**

System:

Intel Q9400 @3200
6 gb DDR2 @800mhz ram
NVIDIA gtx 1070 8 gb
Tensorflow GPU version on Anaconda

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

[image1]: ./upload/undistort_output.jpg "Undistorted"
[image2]: ./upload/warped_chessboard.jpg "Warped "
[image3]: ./upload/binary_combo_example.jpg "Binary Example"
[image4]: ./upload/warped_straight_lines.jpg "Warp Example"
[image5]: ./upload/histogram.jpg "Histogram"
[image6]: ./upload/polynomial_lines.jpg "Polynomail Lines"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. In the carnd-term1-project4-advanced-lane-lines.ipynb, second cell I found all the calibration images Chess Board Corners. Then saved to pickle file (wide_dist_pickle.p) as "objpoints" and "imgpoints".
At third cell calibrated camera using obj and img points found at the 2. cell. Saved "mtx" and "dist" to same pickle file.
At fourth cell applied calibration to camera and saved all the calibrated images as undist_*.jpg. Here is an example of it:

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. At cell six I defined; sobelx, sobely, magnitude, direction and color transform (HLS) and combined them to create binary image as binary_ex.jpg. Here you can see the result:

![alt text][image3]

#### 3. The code for my perspective transform includes a function called `warp_the_image()`, which appears in cell 7 in Ipython notebook. This function takes as inputs an image (`image, save_images=False,plot_result=False,idx=0`). In this function, first I applied the functions mentioned at #2 and created binary image. Then created the points for 'src' considering the center of the image in horizontal axis. Defined 'dst' points. Then used cv2.getPerspectiveTransform to find M, Minv. Created warped image using cv2.warpPerspective. Saved images as 'warped_combined*.jpg'.   

```python
mask_y_height = int(img_shp[0]*0.62) #Upper bound of y on the image
offset=img.shape[0]*0.25 			 

center_x = img_shp[1]//2 
upper_margin = 50
lower_margin = 430
left_up_margin=center_x - upper_margin
right_up_margin=center_x + upper_margin
left_down_margin=center_x - lower_margin
right_down_margin=center_x + lower_margin
    
upper_right=(right_up_margin,mask_y_height)
lower_right=(right_down_margin,img_shp[0]*0.95)
lower_left=(left_down_margin,img_shp[0]*0.95)
upper_left=(left_up_margin,mask_y_height)

src=np.float32([upper_left,
                upper_right,
                lower_right,
                lower_left])

dst=np.float32([[offset,0],
                [img_size[0]-offset,0],
                [img_size[0]-offset,img_size[1]],
                [offset,img_size[1]]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. Here is an example of warped_combined.jpg image:

![alt text][image4]

#### 4. To identify lane-line pixels I created function draw_polyfit(binary_warped,draw_polyfit_lines=False) in cell nine, which takes in an image and calls warp_the_image function. Using this warped image I created histogram and found the location of lane lines according to the density of white pixels. Here is an example of 2 images histogram:

![alt text][image5]

I find position of points of lanes of both right and left side using histogram result. Then divided the image into 9 equal parts to find next points to draw second order polynomial lines.
An example of polynomial fit lines:

![alt text][image6]

#### 5. In cell eleven, created curve_radius(image) function. Which takes in an image and from draw_polyfit function gets ploty points to be used in left and right curvatures of the lanes. 

#### 6. In cell 12, defined lane_area(image) function. Using all the functions mentioned above, found the points 'pts' needed to be fed into cv2.fillPoly() function. Also found the place of the car according to the center of the lane in this lane_area() function. 

---

### Pipeline (video)

#### 1. Here's a [link to my video result](https://drive.google.com/open?id=0B1qa2SOuBDHOOGpLajFzQXk5blE)

---

### Discussion

#### 1. Had too much problem while deciding best points for src. Even small changes caused unparallel lines, which caused separated lane lines at warped_combined. Lost too much time on finding proper src points. After deciding decent points, other problems have been solved. 



