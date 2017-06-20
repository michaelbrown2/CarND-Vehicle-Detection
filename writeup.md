##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car.png
[image8]: ./examples/noncar.png
[image2]: ./examples/HOG_example.png
[image9]: ./examples/HOG_noncar.png
[image3]: ./examples/sliding_windows.png
[image4]: ./examples/sliding_window.png
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

See section labeled HOG Function / Test HOG Function

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of 25 each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]
![alt text][image8]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `LUV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]
![alt text][image9]

####2. Explain how you settled on your final choice of HOG parameters.

I tried many different parameters and colorspaces and found LUV to be the most effective colorspace and the given HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` to be sufficient
####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

See the sections labeled Train SVC and Test SVC to see my SVC training.  I used spatial binning, LUV color space, and of course the HOG to train my SVC.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

See the section labeled Sliding Window for my code to implement the sliding window on the test images.  I ended up with three scales in the end with three different search windows to get the best results.

![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using LUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output_with_lines.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:

I attempted to integrate an averaging heat map like this but had no luck, I minimized false positives using the threshold on the heat map and used the heatmap to combine multiple hits into one boxed area.

### Here the resulting bounding boxes are drawn onto the last frame in the series:


---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I had many issues while writing this program, as I mentioned before I was getting a 0% hit rate on my prediction of cars because I had stacked my features in my pipeline differently than I had when training.  I also had it working VERY well, 0 false positives, and went to tune it to smooth out the boxes and somehow forgot / lost my original configuration and it took a very long time to tune it back to a usable program.

I also had issues with colorspace, it seemed like everytime I switched colorspace, it was better than the last one, but i ended up just switching back and forth between YCrCb and LUV

Also, my laptop is not the fastest in town and I'm sure my algorithm is very ineffecient but processing the full length video is almost 45 minutes long now. The short test video is quick, but every time I tune something it works perfectly in the test video but false positives show up in the full project video.

####2. Above and beyond!

I implemented my advanced lane finding algorithm into this as well, I think overall it works beautifully, even if I'm still having issues with an occasional false positive

I want to go back eventually and rework it so it averages heatmapping over multiple frames, but my python is fairly weak and my attempts at implementing it were all complete failures.

