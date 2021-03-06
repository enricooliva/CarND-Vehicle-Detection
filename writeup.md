**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/sliding_windows2.png
[image4]: ./output_images/sliding_windows.png
[image5_1]: ./output_images/siximages_heatmap1.png
[image5_2]: ./output_images/siximages_heatmap2.png
[image5_3]: ./output_images/siximages_heatmap3.png
[image5_4]: ./output_images/siximages_heatmap4.png
[image5_5]: ./output_images/siximages_heatmap5.png
[image5_6]: ./output_images/siximages_heatmap6.png
[image6_1]: ./output_images/siximages_label1.png
[image6_2]: ./output_images/siximages_label2.png
[image6_3]: ./output_images/siximages_label3.png
[image6_4]: ./output_images/siximages_label4.png
[image6_5]: ./output_images/siximages_label5.png
[image6_6]: ./output_images/siximages_label6.png
[image7_1]: ./output_images/siximages_cars1.png
[image7_2]: ./output_images/siximages_cars2.png
[image7_3]: ./output_images/siximages_cars3.png
[image7_4]: ./output_images/siximages_cars4.png
[image7_5]: ./output_images/siximages_cars5.png
[image7_6]: ./output_images/siximages_cars6.png
[image8]: ./output_images/sliding_windows1.png
[video1]: ./result.mp4


### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (Vehicle_Detection.ipynp) in lines 91 through 99.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I take the two images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and channels. The final choice is based evaluating the result of the classifier in term of accurancy and predictions. The final parameter chosen are based on YCrCb color space and ALL channels of the color space.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained the classifier, linear SVM using the following parameters:

color_space = 'YCrCb' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 9  # HOG orientations
pix_per_cell = 8 # HOG pixels per cell
cell_per_block = 2 # HOG cells per block
hog_channel = 'ALL' # Can be 0, 1, 2, or "ALL"
spatial_size = (32, 32) # Spatial binning dimensions
hist_bins = 32    # Number of histogram bins
spatial_feat = True # Spatial features on or off
hist_feat = True # Histogram features on or off
hog_feat = True # HOG features on or off

The classifier code is in the third block of `Vehicle_Detection.ipynp` file. It exploit the `extract_features` function to extract features from a list of images. Than the feature list are divided in training and test sets and used in the Support Vector Classifier through fit function. After that the results are stored in `svc_pickle.p` file line 97.

Using: 9 orientations 8 pixels per cell and 2 cells per block
Feature vector length: 8460
261.41 Seconds to train SVC...
Test Accuracy of SVC =  0.9904

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search in a delimited area of the images escluding the hight part
ystart = 400
ystop = 656

For the slinding window implementation I exploit the function find_cars provided in the lesson material that's able to both extract features and make predictions. It is more efficient method for doing the sliding window approach.

The scale used is 1.5 with a windows overlap of 0.75%.

In the figure an example of the resulting bounding boxs:

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
![alt text][image8]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./result.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on test images.

### Here are six frames and their corresponding heatmaps:

![alt text][image5_1]
![alt text][image5_2]
![alt text][image5_3]
![alt text][image5_4]
![alt text][image5_5]
![alt text][image5_6]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6_1]
![alt text][image6_2]
![alt text][image6_3]
![alt text][image6_4]
![alt text][image6_5]
![alt text][image6_6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7_1]
![alt text][image7_2]
![alt text][image7_3]
![alt text][image7_4]
![alt text][image7_5]
![alt text][image7_6]


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In this project, I write a software pipeline to identify vehicles in a video from a front-facing camera on a car. 
In this approach I used a SVM classifier applyied on the extrating features from the images: HOG features, spatial features and histogram features. I think the next step is to use a deep learning approach for classification problem instead the SVM classifier.

