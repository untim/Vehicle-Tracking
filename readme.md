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
[image1]: ./output/False_Positives.png
[image2]: ./output/Final_pic.png
[image3]: ./output/Gray_basic_hog.png
[image4]: ./output/Gray_with_orientations.png
[image5]: ./output/Heatmap.png
[image6]: ./output/LUV.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the 21st code to the 30th code cell in the iPython Notebook. First of all I read in all the pictures and saved them as arrays as non car and car images. Then I shuffled the arrays and as I noticed that I had more car data than no car data I only used the same amount of car data as I had non car data. This was done as we learned that this may influence the classifier. Then I also created labels for both the car and non car data and concatenated the both list.
Next I used the train_test_split function to separate the data in to a train, validation and test dataset and had it shuffled once again.



I then explored different color spaces.

I started off looking at the different colour spaces of car photos and non car photos in a 3D plot with the functions made available by Udacity. There I noticed that LUV tended to show very good results for for seeing the difference between these  photos. Thus I decided to use these photos as for my spatial and histogram features in the classifier.

![alt text][image6]

For the decision which colour space to take for hog I plotted all available hog transformations for HSV, HLS, YUV, LUV and GRAY. There I noticed that I especially liked the grey colour space as it performed quite well and I saw a lot of features



![alt text][image3]

#### 2. Explain how you settled on your final choice of HOG parameters.

For this I plotted multiple orientations, pix_per_cell, cell_per_block against each other for the grayscale hog image and then decided which one was best. This was difficult.
The pitures with orientation of 10 look more detailed and the cars be seen very clearly however it can also be seen that a lot of features in the photos are lost and more features can be seen in the photos with  orientation 9
There the picture with pix_per_cell: 8 and cell_per_block_2 looks the best
So we choose the gray color channel with orientation 9 with pix_per_cell: 8 and cell_per_block_2

![alt text][image4]


#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a neural network in code cell 31 with multiple layers. I used an adam optimiser to reduce overfitting and adapt the learning rate automatically  used dropout layers to reduce overfitting even more.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to stick with the first algorithm supplied by Udacity which allowed to choose the xy window size, the xy overlap the x start and stop and the y start and stop.
I began from the bottom of the box and manually fitted a box which could be big enough to fit a car in the front of the video (code box 37). With this I continued until I thought the boxes where small enough and all cars should be found with them.
I used a large overlap as I thought that if there really a car a large overlap would lead to the scenario that there a multiple windows around the car which would create a lot of heat later on in the process.

![alt text][image1]

The corresponding heat map:

![alt text][image5]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on the grey hog image, the LUV spatial and histogram features which resulted in very good results and a testing accuracy of the neural network of 98,4%. Furthermore for smoothing the heatmaps and the resulting boxes I averaged out the heatmaps over the last 5 heatmaps which should also reduce the false positives.
The examples can be seen above.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's the link to the video.https://youtu.be/32zcEIHnibk
The video performs reasonably well however sometimes some false positives appear but quickly disappear again. A problem is also that the false boxes to identify the cars most of the time but are not very tight around the car and sometimes the two cars are identified with one box




#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. Furhermore I smoothed the heatmaps by taking the mean over the last few heatmaps which resulted in somewhat better accuracy  

Here is an example of the heatmap.

![alt text][image5]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

This project was very difficult as many aspects had to be taken into consideration. First of all it began by choosing the right classifier with the right features, with the right window sizes and thresholds……theres endless possibilities. I believe my pipeline likely fails with multiply car next to each other as the are either bound in one window or sometimes not bound at all. Furthermore if the edge on the side of the lane has one specific look the classifier and all the corresponding factors have a hard time distinguishing from car and non car.
I believe it could be more robust my introducing an almost perfect classifier with different windows which are perfect in size for their respective position in the picture.

