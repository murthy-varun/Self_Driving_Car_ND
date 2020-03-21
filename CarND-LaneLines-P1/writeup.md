# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/final_solidWhiteRight.jpg "Final Solid White Right"
[image2]: ./test_images_output/pipeline.jpg "Pipeline Solid White Curve"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of following steps.
1. Extracting only white and yellow colors from the image. This was achieved by converting image to HSV color space and masking the color range. 
This was especially needed for optional challenge wherein the left solid yellow line was not being detected if this step was skipped.
2. Converting image to grayscale 
3. Applying Gaussian Blur to remove noise (kernel size=7)
4. Apply canny edge detection (low_threshold=100 & low_threshold=150)
5. Apply region of interest mask
6. Apply Hough tranform (rho=1, theta=1 radian, threshold=25, min_line_length=5, max_line_gap=2)
7. Identify if the line segments belong to left line or right line based on slope. Draw the left and right lines by averaging the slope and intercept of left line and right lines.
8. Draw the lines on the original image

Image transformation as it passes through the pipeline

![pipeline image][image2]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by 
1. Set y_min and y_max as height of image
2. For each line in lines
  * Find slope of the line. If vertical line, then slope = -infinity
  * Classify the line as left line is -infinity < slope < -0.5 (~153 degrees). Save the left line's x,y co-ordinates, slope and intercept.
  * Classify the line as right line is 0.5 < slope < infinity (~27 degrees). Save the right line's x,y co-ordinates, slope and intercept.
  * Ignore all other lines. Eliminates outliers. 
  * Check if any of the y co-ordinates of the line are lesser than y-min, then make that as y_min (to find highest point of the line in the image).
3. If left line's co-ordinates exists, the draw the left line by averaging the left line's slope & intercept, y_min and y_max.
4. If right line's co-ordinates exists, the draw the right line by averaging the right line's slope & intercept, y_min and y_max


Output videos for the 3 test videos are available at:
* [!Test video 1 - Solid White Right](https://github.com/murthy-varun/Self_Driving_Car_ND/blob/master/CarND-LaneLines-P1/test_videos_output/solidWhiteRight.mp4)
* [!Test video 2 - Solid Yellow Left](https://github.com/murthy-varun/Self_Driving_Car_ND/blob/master/CarND-LaneLines-P1/test_videos_output/solidYellowLeft.mp4)
* [!Test video 3 - Challenge](https://github.com/murthy-varun/Self_Driving_Car_ND/blob/master/CarND-LaneLines-P1/test_videos_output/challenge.mp4)

### 2. Identify potential shortcomings with your current pipeline
This pipeline currently uses hard-coded parameters which may not work in different different conditions for example day vs evening vs night, sunny vs rainy vs snowy conditions etc.
Another potential issue is if the camera's position changes, the ROI mask that needs to applied will need to be changed as well.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to have a set of parameters for different conditions and adaptively use the parameter set for detected conditions.
