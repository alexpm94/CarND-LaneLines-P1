# **Finding Lane Lines on the Road** 

## Writeup
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/curve.png "Dashed curve"

---

### Reflection

### 1. Pipeline

My pipeline consisted of 6 steps. 

1. Convert image to grayscale
2. Eliminate noise
3. Identify edges
4. Define ROI
5. Identify lines
6. Draw lines in the original image

In order to detect lane lines, firstly I convert to grayscale. This is because of canny detector.
![alt text][image1]

In order to avoid noise, I applied 3 techniques.
Firstly I applied a gaussian filter to blur the image, because there are some undesired white points on the road that could be marked as lines. Applying a blur make these points darker so that when applying canny, the threshold does not consider these points.

I also applied a technique called "Opening". This mean apply an erosion followed by a dilation, in order to avoid white spots again.

Then I applied canny, I select the region of interest (part of the image that I care about) followed by hough transform, and here is where the difficult part appears.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by detecting separately right and left lines.
I made this separation by measuring the slope of the lines. As there are some horizontal lines on the videos (perpendicular to left and right lines). I decided to eliminate them because they were causing noise when doing the extrapolation. 
I just set boundary slopes of 0.5 and -0.5 for left and right lines respectively.
Then I created a extrapolate function, that takes 3 parameters: x,y,h. The variables X,Y are vectors and h is the height of the image. As the boundary values of Y can be fix (set by h) for every picture. 
I created a lineal regression using polyfit of x respected to y. This function return 2 points (x1,y1,x2,y2), that will be used to draw a line from (x1,y1) to (x2,y2).


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there are shadows present or it's too sunny, as the threshold is not adaptative to this.

What about a car in front of us? It would be insde our ROI and so it would extract information from it (edeges, lines). It'd introduce a lot of noise to our lane detetctor.

Another shortcoming are close curves, as my algorithm draw lines using just 2 points. I tried to used polyfit(x,y,2), in order to aproximate to a 2nd degree equation and using linspace to draw 50 lines, but it was not acurately at all for dashed lines.

![alt text][image2]



### 3. Suggest possible improvements to your pipeline

A possible improvement would be to implement an adapative tresholding like CLAHE, for adaptative histogram equalization. Or the use of HSV instead of RGB. In order to solve the lighting problems.

Another potential improvement could be to segment lane lines into 5 parts. And create a 1D lienar regression for each part, so that when detecting a curve, lane lines could be drawn in 5 segments or even more segment to make it smoother.
