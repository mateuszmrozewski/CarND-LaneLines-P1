
# Finding Lane Lines on the Road

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on my work in a written report

The final result can be seen [here](http://htmlpreview.github.io/?https://github.com/mateuszmrozewski/CarND-LaneLines-P1/blob/master/P1.html).

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The starting point of the pipeline where the functions already
provided in the notebook. Using them in the correct order gave
a good enough starting point for the final implementation. After
playing around with the test images and videos (especially the
  challenge one) the final shape of my pipeline is:

1. color_range - this step filters out white and yellow colors
   using the HSV color model and mixes the result with the original image using the weighted_img function. This step allows me to enhance the lines in difficult situations like in the challenge video (yellow line on a light colored tarmac).
1. grayscale - converts image to grayscale
1. gaussian_blur - used to reduce noise
1. canny - used to detect edges. After various experiment I sticked with thresholds of 80 and 120. Initially I tried more rigorous values but it didn't play well with corner cases (again, challenge video).
1. region_of_interest - this is the moment when we can focus only on road.
1. hough_lines - now we try to match the points to from canny output to get actual lines. Tuning these parameters seemed to be the most challenging. I am not fully convinced that my values are optimal. However they work good enough with the videos.
1. extrapolated - this step seems to be crucial one. I first filter out lanes with slopes not matching the side of image and that are to close to be horizontal. Next I used linear regression to get the model for left and right line and then predict the values for x cooridnates that roughly match the masked region (our POV).
1. weighted_img - apply the extrapolated lines onto the original image

draw_lines was left unmodified and it just draw lines.

### 2. Identify potential shortcomings with your current pipeline

One of the shortcomings is that I discard completely lines that don't match roughly the expected slope. This might by fine in the highway situation but it should  not do that in the city. Some of the lines will be horizontal and we should see them as well.

Another shortcoming is that the estimated line is straight while it might be better to try to estimate a curve to cover bends as well.

Also filtering input lines could be improved to get rid of some short edges or marking that are not the lines themselves.

### 3. Suggest possible improvements to your pipeline

Looking at the videos, especially the challenge one, we can see that sometimes lines jump for a frame or two to a new position. One solution would be to implement memory in the process, like in the GPS systems. Current frame would be calculated based on the observations and previous frame. This way a sudden spark or reflection that would cause drawing a line in wrong position would be reduced - a line would move only a little if previous and current position differs a lot.

To estimate position of a new line I used linear regression. Maybe with using another approach I could get better results. For example estimating a and b parameters of y=ax+b might give better results and would allow better outlier detection.

Another possible improvement could be searching for almost parallel and close pair of lines. This should help to filter out the noise as well.

I also believe that spending more time on image preprocessing could allow Canny and Hough algorithms to work better.
