#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline built off of the examples from the lessons for lane detection. First, as shown on the lessons, it converts the image to grayscale and performs a Canny transform. Next I chose to set the region of interest using percentages of the total image dimensions in order to get the same relative region regardless of image size. In the challenge video there is a section of the car's hood that appears in the bottom of each frame. To prevent this from being interpreted as a line, I also cropped out this region. Next I used the cv.HoughLinesP class to get a collection of lines, which were then drawn on the original image. 

Instead of modifying the line drawing function, I decided to average the lines using Sklearn's K-Means clustering package. In order to do this, I needed to transform the lines back into Hough space so that similar lines would become points that could be clustered together. I decided to create my own simple Hough Transform function. My function converts from (x,y) space to (m,b) space. I think that I may have gotten better performance if I had converted to polar, because I did end up having a lot of issues dealing with vertical lines, which would cause the clustering algorithm to crash due to infinite values (and other similar issues).

I included in my notebook graphical representations of my Hough transform and clustering processes that help to clarify what I did. 

After implementing k-means clustering (and settling a bunch of preprocessing issues), my algorithm was working great on the first two videos, but not very well on the third. In an attempt to better understand the issues, I implemented a color-code system that is based on the inertia (error) of the clustering function. Green lines represent a low inertia value, indicating that the algorithm in seeing exactly two distinct lines. Yellow and red lines signify progressively higher inertia, indicating that the algorithm is seeing more than two distinct lines. 

I set the clustering function for n=2 clusters. However, especially in the challenge video, the pipeline would often detect line segments that were from other image features such as variations in the pavement. In my k_means_clustering function I attempted to get rid of these false lines by momentarily increasing the number of clusters (n) to three for frames with inertia values above a certain threshold. Then I would drop out all lines that were from the smallest clusters and re-cluster with n = 2. 

This strategy significantly improved my performance on the challenge video. However the algorithm still has a difficult time with that video, especially when the car drives over the spot where the type of pavement switches.



###2. Identify potential shortcomings with your current pipeline

My model has a hard time when there are other linear color gradients in the region of interest. If a line is above certain thresholds it is indecernable from a lane line. I can think of two ways to potentially improve performance. First, I could continue to modify the threshold parameters that determine what edged make it into the initial Hough transform. Second, I could implement further filtering after the initial Hough transform. For instance, I could filter out all lines whose slopes are below a certain magnitude. This would get rid of lines that are running approximately perpendicular to the direction of travel. 

Another weakness of my pipeline is that it can fail due to data formatting errors. I have not yet learned enough about the k-means algorithm or about sklearn's preprocessing modules to have correctly implemented a very robust algorithm. I have had a variety of errors come out of the k-means function and I am confident that I have not fixed all possible bugs. 


###3. Suggest possible improvements to your pipeline

Discussed above. 