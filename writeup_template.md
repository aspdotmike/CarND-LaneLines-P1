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

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I applied a gaussian blur to smooth the image. This is additional smoothing since the canny function already applies a gaussian blur internally.
Next I used the canny function to produce an image where only the edges are displayed. This is done by modifying the pixles so that only the pixels that are positioned in the area of steepest descent are white. The other pixels are turned to black. 
Next the pipline takes as in put the image produced by the canny function and applies a mask to narrow down the area I wanted the code to focus on. This is done by applying a polygon mask that covers the left and right lane in front of the car. I am able to rely on a constant polygon because the camera on top of the car is in a fixed position when the vehicle is in motion. 
Once I had a masked image of the canny function the next thing to do is draw lines on the image. To do this I applied the hough transform. Each of the white pixels in the imgage space has infinite lines going through them. To find the lines to draw in image space the hough transform represents in the image in hough space using polar coordinates. Where the functions intersect in hough space provides the rho and theta for the line in image space that connects pixels. Once I had the lines then I went ahead and drew the lines on the original image. 


In order to draw a single line on the left and right lanes, I modified the draw_lines() to include four key steps.
First: organize the different lines I identified from applying the hough transform into two categories (left line and right line). I did this by looking at the slope of the line. If the line had a positive slope then it represents the right side of the lane. If the line had a negative slope then it represents the left side of the lane. 

Second: For all the lines that made up one side of the lane, I grouped all the x and y values of the line end points into an array containing only the x values and y values. 

Third: I used a numpy function to help identify a line that best fit all these points. 

Fourth: Once I had both the m, b value for the the left and right lane, I then wanted to draw a line that went from the bottom boudnry of the image to the top boundry of the polygon I used for the mask. To do this was pretty straight forward given that I had the function for the left and right lane as well as I had the y values for what would be the endpoints for the lines that would extend boundry to boundry. 

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen if the lines are not so clearly defined. I noticed while I was adjusting the parameters for the hough function, at times I would get an error at a specific frame in the image. My assumption is that because of the threshold setting I had there were no lines detected and it caused an error. 
Also, a lot of what I had done was dependent on the grayscale image that was produced from the original image. My understanding is that in certain lighting conditions this could affect how the lines may appear. For instance the yellow line in a very dark day might not show in the gray scale image. I think this is where the inRange function comes in. I saw this link which I'm trying to understand more deeply https://www.learnopencv.com/color-spaces-in-opencv-cpp-python/



### 3. Suggest possible improvements to your pipeline

To make the extrapolation of the line more adaptive to the context of the image. If there's a car that's really close in front of me, I think I wouldn't extrapolate to the upper boundry of the polygon mask. Instead I'd cap it at the max_y value of the line I got from the hough transfrom. 

Another potential improvement could be to use the inRange function. Looking at the challenge video I saw some frames where the pavement turned really bright alongside the yellow line. I think there is some improvements that could make the code more robust to identify the lines in various lighting conditions. 
