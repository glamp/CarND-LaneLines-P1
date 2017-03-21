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

My pipeline consisted of 7 steps:

- converted the image to grayscale.
- ran the grayscale image through a the Gaussian smoothing function with `kernel=5`.
- ran the smoothed image through a Canny edge detection function using a `low_threshold=50` and a `high_threshold=150`.
- applied a quadrilateral image mask to the resulting edges
- performed a Hough transform on the result using `rho=2`, `theta=1 rad`, `threshold=15`, `min_line_length=40`, `max_line_gap=20`
- calculated lines for each lane and applied them to the image

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by:

I split the resulting lines from the Hough transform into "left" and "right" lines. Left lines were lines with a x1 on the left half of the image and right lines the opposite. I tossed out any lines that had a slope with a magnitude > 1 or < 0.5.

With the remaining left and right lines, I decided to fit a linear regression model to approximate each. I wanted to do weighted regression so that longer lines would count for more in my resulting fit. First I "filled in" the starting and ending points, (x1, y1) & (x2, y2), with intermediary points. For example, (1, 2) & (3, 4) would become: [(1, 2), (2, 3), (3, 4)]. Doing this made the longer lines more valuable in the resulting linear regression. So the easier to detect lines near the bottom of the image would count for more in the resulting fit.

The last step was to add each line to the image. I used the equation for each line to find their intersection (if applicable). I took the y values of the intersection and increased them by 5% so that my lines would stop a little before they intersected in the image (I realize this sounds a little strange since the images are "upside-down" with regard to a typical coordinate system). I used this y-value as the y2 value for my line. I used the max-y value of the image as my y1 value

If you'd like to include images to show how the pipeline works, here is how to include an image:

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

My method seems to be a bit "jittery" when run on the images. I think this might be because I'm not smoothing the image enough.

Another issue is that my parameters for each algorithm are fixed. So my pipeline works well on the videos and images provided, but struggles with the challenge problem at times.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use a polynomial with `order=2` instead of a linear regression. This might make it easier to detect curves in the road, such as in the challenge video.

Another potential improvement could be to increase the amount of smoothing used. My lines were jittery at times and I think playing with some of the parameters would have yielded less noise.
