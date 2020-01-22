# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of the following steps:

1) Convert RGB to grayscale: This will return an image with only one channel
2) Apply a Gaussian blur to smooth the image and remove the noise:  Ihave used the  kernel_size = 5 in this project
3) Perform Canny edge detection:  An edge is detected where there is a sharp change in the intensity of image pixels, such as going from black to white color. I have used the low_threshold = 50 and high_threshold = 150. Any edge with intensity gradient of more than 150 will be considered and any one with intensity gradient of less than 50 will be discarded. 
4) Define a region of interest and remove the undesired portions of the image
5) Apply Hough transformations to the extracted mask and retrieve the desired lines: The parameters I used are:  rho = 1    theta = np.pi/180    threshold = 35    minLineLength = 5    maxLineGap = 2
Where, threshold is the minimum number of votes required to consider the edge as a line, minLineLength is the minimum length of line which will be accepted and maxLineGap is the maximum allowed gap between line segments to consider them as one single line.

6) Apply lines to the original image: The Hough lines are a collection of lines that include both the left and right line. The line equation is shown as: y = mx+b, where m is the slope and b is the y intercept

# Using the slope value of lines to filter right and left lines 

 I calculated the slope of each Hough line and noticed that any negative slope belongs to the left line and positive slopes belong to the right line. Therefore I separated the left and right lines using this technique. I also calculate the angle value for each line and discarded any line with an absolute angle value less than 15 degree or more than 40 degree. These values chosen by trial and error and noticing the approximate angle value that lanes make in the image. 
I also calculate the y intercept of each line using the previously calculated slope and equation b = y – mx.
Once the b value for each line is determined,  I calculated the median and standard deviation of all the values. The cut off value is determined to be the two times the standard deviation and lower and upper threshold is defines as:
cut_off = data_std * 2
lower, upper = data_median - cut_off, data_median + cut_off
 Any lines with b intercept less than lower and more than upper are discarded. 
After removing the outliers, it was time to create a single line. I calculated the average value of slopes and intercepts to get the slope and intercept for the single line representing the lanes. Then used a function to calculate the x and y points of that single line. The y values are assumed to be one at the bottom of the image and the other a bit over the middle of the image. Based on the known y values, the x values are calculated. 
At the end, I applied these single lines to the original image. The pipeline worked well for the first two videos and I was able to get lines running along both the right and left lanes. However, the pipeline did not work for the challenge video.



### 2. Identify potential shortcomings with your current pipeline

- The parameter tunings require some level of experience and trial and error. Especially at Hough transform where there are many parameters to be adjusted.
- The algorithm I developed here is not robust when the road conditions changes abruptly such as sudden curvy road or when there was shadow over the road or when there was a bit of discoloration of the lines. 
- This algorithm was developed based on the assumption that there is no car in front of the road where we are driving. Other conditions such as the presence of rain or snow can also affect the results and this method might not be that robust for thses conditions.





### 3. Suggest possible improvements to your pipeline


- I would like to improve my pipeline for the challenge video. I think I can change the region of interest and make it tighter and at the same time I should be able to save previous lines as the history of the lines. When the pipeline is unable to create the lines, I can ask the program to use the previously calculated lines buffered in the history.

- Fitting a polynomial line instead of the straight line can also improve the results especially for the curvy lanes

- Another way to improve is converting to HSV color-space. This will help to boost the yellow area of the image. I have not tried this method yet but planning to work on that too.
