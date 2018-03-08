# Adv-Face-Detection


## Overview

The sliding window model is conceptually simple: independently classify all image patches as being object or non-object. Sliding window classification is the dominant paradigm in object detection and for one object category in particular -- faces -- it is one of the most noticeable successes of computer vision. For example, modern cameras and photo organization tools have prominent face detection capabilities. These success of face detection (and object detection in general) can be traced back to influential works such as [Rowley et al. 1998](https://course.cse.ust.hk/comp5421/Password_Only/projects/faces/rowley_pami_1998.pdf) and [Viola-Jones 2001](https://course.cse.ust.hk/comp5421/Password_Only/projects/faces/viola_Jones_IJCV_2001.pdf). You can look at these papers for suggestions on how to implement your detector. However, for this project you will be implementing the simpler (but still very effective!) sliding window detector of [Dalal and Triggs 2005](https://course.cse.ust.hk/comp5421/Password_Only/projects/faces/dalal_triggs_cvpr_2005.pdf). Dalal-Triggs focuses on representation more than learning and introduces the SIFT-like Histogram of Gradients (HoG) representation (pictured to the right). You will not be asked to implement HoG. You will be responsible for the rest of the detection pipeline, though -- handling heterogeneous training and testing data, training a linear classifier (a HoG template), and using your classifier to classify millions of sliding windows at multiple scales. Fortunately, linear classifiers are compact, fast to train, and fast to execute. A linear SVM can also be trained on large amounts of data, including mined hard negatives.

## Details and Starter Code

The following is an outline of the stencil code:

- `proj4.m`. The top level script for training and testing your object detector. If you run the code unmodified, it will predict random faces in the test images. It calls the following functions, many of which are simply placeholders in the starter code:
- `get_positive_features.m `(you code this). Load cropped positive trained examples (faces) and convert them to HoG features with a call to `vl_hog`.
- `get_random_negative_features.m` (you code this). Sample random negative examples from scenes which contain no faces and convert them to HoG features.
- `classifier training` (you code this). Train a linear classifier from the positive and negative examples with a call to `vl_trainsvm`.
- `run_detector.m` (you code this). Run the classifier on the test set. For each image, run the classifier at multiple scales and then call `non_max_supr_bbox` to remove duplicate detections.
- `evaluate_detections.m`. Compute ROC curve, precision-recall curve, and average precision. You're not allowed to change this function.
- `visualize_detections_by_image.m`. Visualize detections in each image. You can use `visualize_detections_by_image_no_gt.m` for test cases which have no ground truth annotations (e.g. the class photos).

Creating the sliding window, multiscale detector is the most complex part of this project. It is recommended that you start with a *single scale* detector which does not detect faces at multiple scales in each test image. Such a detector will not work nearly as well (perhaps 0.3 average precision) compared to the full multi-scale detector. With a well trained multi-scale detector with small step size you can expect to match the papers linked above in performance with average precision above 0.9.



## Data

The choice of training data is critical for this task. While an object detection system would typically be trained and tested on a single database (as in the Pascal VOC challenge), face detection papers have traditionally trained on heterogeneous, even proprietary, datasets. As with most of the literature, we will use three databases: (1) positive training crops, (2) non-face scenes to mine for negative training data, and (3) test scenes with ground truth face locations.

You are provided with a positive training database of 6,713 cropped 36x36 faces from the [Caltech Web Faces project](http://www.vision.caltech.edu/Image_Datasets/Caltech_10K_WebFaces/). We arrived at this subset by filtering away faces which were not high enough resolution, upright, or front facing. There are many additional databases available For example, see Figure 3 in [Huang et al.](https://course.cse.ust.hk/comp5421/Password_Only/projects/faces/lfw.pdf)and the [LFW database](http://vis-www.cs.umass.edu/lfw/) described in the paper. You are free to experiment with additional or alternative training data for extra credit.

Non-face scenes, the second source of your training data, are easy to collect. We provide a small database of such scenes from the [SUN scene database](http://groups.csail.mit.edu/vision/SUN/). You can add more non-face training scenes, although you are unlikely to need more negative training data unless you are doing hard negative mining for extra credit.

The most common benchmark for face detection is the CMU+MIT test set. This test set contains 130 images with 511 faces. The test set is challenging because the images are highly compressed and quantized. Some of the faces are illustrated faces, not human faces. For this project, we have converted the test set's ground truth landmark points in to Pascal VOC style bounding boxes. We have inflated these bounding boxes to cover most of the head, as the provided training data does. For this reason, you are arguably training a "head detector" not a "face detector" for this project.

Copies of these data sets are provided with your starter code and are available in the starter code. You probably want to make a local copy of these to speed up training and testing, but please do *not* include them in your handin.