## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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
[vehicle_image]: ./output_images/vehicle_image.png
[vehicle_hog]: ./output_images/vehicle_hog.png
[slidingwindows]: ./output_images/slidingwindows.png
[image4]: ./output_images/sliding_window.png
[vehicle_detected]: ./output_images/vehicle_detected.png
[vehicle_detected_process]: ./output_images/vehicle_detected_process.png
[image7]: ./output_images/output_bboxes.png
[video1]: ./output_images.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell [4]-[6] of the IPython notebook ('CarND_Vehicle_Detection_fuqiang.ipynb').

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][vehicle_image]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][vehicle_hog]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and the results are shown as below:

label | colorspace | orient | pix_per_cell | cell_per_block | hog_channel | accuracy | time
:---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
1 | RGB | 9 | 8 | 2 | ALL | 0.9823 | 109
2 | HSV | 9 | 8 | 2 | ALL | 0.9907 | 91
3 | HLS | 9 | 8 | 2 | ALL | 0.9899 | 94
4 | YCrCb | 9 | 8 | 2 | ALL | 0.9899 | 94

Due to limited time, I failed to try more combinations. But the accuracy for these combinations is well enough. Hence I choose the parameters of label 4 for the following training.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using HOG features, spatial features and histogram features, as shown in the code cell [6]-[9] of the IPython notebook ('CarND_Vehicle_Detection_fuqiang.ipynb'). The accuracy is almost 0.99.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I used the Hog Sub-sampling Window Search method presented in the lecture, as shown in the code cell [24] of the IPython notebook ('CarND_Vehicle_Detection_fuqiang.ipynb') 

![alt text][slidingwindows]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][vehicle_detected_process]

More examples can be found below:
![alt text][vehicle_detected]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

To make the results more smooth, I add a class Bboxes_Store to store the most recent boxes identified in previous frames, as in the code cell [31]-[32] of the IPython notebook ('CarND_Vehicle_Detection_fuqiang.ipynb'). This is helpful to use the results of previous frames and generate more smooth and robust results.



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think there are some disadvantages for this approach:
1) it is art of work to extract features from images, since there are too many parameters
2) the windows detected do no match size of vehicles so tightly

I could improve it via the following ways in the future:
1) try YOLO algorithm
2) learn more from others about computer vision and feature engineering

