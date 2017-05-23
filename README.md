# Vehicle Detection for Self-Driving Cars

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

In this project (#5 in Udacity's SDCND) I develop a pipeline to detect other vehicles on the road from images taken within a driving vehicle. Both machine leaning and computer vision techniques are utilized. The pipeline processes single images but can also be applied to frames of a video.

If you want to toy with the code, it's all in [pipeline.ipynb](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/pipeline.ipynb). The code is well-documented and there's lots of supporting text to walk you through the notebook. [Make sure you get all the dependencies installed. My repo of [Udacity's starter](https://github.com/SealedSaint/CarND-Term1-Starter) might help.] 

### Overview

1. Extracting Features
2. Training a Classifier
3. Sliding Window Search
4. Vehicle Detection and Heatmaps

### Extracting Features

To identify vehicles in an image, we need an idea of what exactly we are looking for - we need a vehicle "signature." This signature must ultimately come from the pixel values themselves, because that's all the information we have to deal with. To create the vehicle signature, I extracted three different feature-sets:

1. Spatial Features
2. Color Histogram Features
3. HOG (Histogram of Oriented Gradients) Features

##### Spatial Features

Spatial features are essentially the pixel values themselves. The image is resized and flattened out, and these values are used directly in our "signature." I used RGB values for this feature-set.

##### Color Histogram Features

A color histogram totals the number of pixel values that fall into X evenly-distributed bins for the image's color spectrum. These "color bin" totals are added on to the vehicle signature. I chose to use 32 color bins and images in the HSV color space for this feature-set.

##### HOG (Histogram of Oriented Gradients) Features

HOG features can basically be thought of as "colors are changing by X amount in Y direction" (hence "oriented gradients"). Detailed tutorials are widely available on the web if you desire to know the intricacies. HOG features make up the bulk of the vehicle signature and are the most valuable features gathered. I passed HSV images to this feature extraction method. More details on the parameters I chose for this method can be found in [pipeline.ipynb](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/pipeline.ipynb).

### Training a Classifier

For each image we wish to train our machine learning classifier on, these features will be extracted and concatenated together to form the image's vehicle "signature." I trained a Linear SVM classifier on over 17,000 images (half of which contained vehicles). The features were normalized before being passed in to the classifier. The test results showed over 99% accuracy.

Below is a random vehicle image the classifier was trained on, with the raw and normalized features extracted for that image.

![Sample Image with Raw and Normalized Features](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/example_images/Raw_and_Normalized_Features.png)

### Sliding Window Search

A trained classifier does no good unless you give it similar information on which it was trained. I trained the SVM on 64x64 pixel images, so I needed a way to pass it 64x64 cutouts of the larger images coming from the dash-cam. Specifically, I want the SVM to be passed 64x64 cutouts of the parts of the image that *have cars* so they can be identified. The closest I can get to knowing where the cars will be in the image without using the classifier is the bottom half, roughly. Thus, I performed a sliding window search in the bottom half of the image to produce windows that will be tested to see if they contain a vehicle. 

Original Image | Windows Searched (Size by Color)
:---: | :---:
![Original Image](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/test_images/test5.jpg) | ![Windows Searched Drawn on Image](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/example_images/windows_drawn_test5.jpg)

### Vehicle Detection and Heatmaps

The sliding window search produces many windows - potentially hundreds! Each of these windows is an image that will be tested with the classifier, meaning each image needs to have features extracted for it. This is the main bottleneck of the pipeline.

Nevertheless, once features are extracted for each image and classified, the windows containing vehicles remain. Unfortunately, these windows usually don't produce nice, single bounding boxes around the vehicles like we want. To reach a single bounding box per vehicle a heatmap is implemented, and a new bounding box is drawn around the "hot" areas. Results before and after the heatmap are shown below.

Windows with Vehicles | Vehicle Windows After Heatmap
:---: | :---:
![Windows with Vehicles](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/example_images/good_windows.jpg) | ![Vehicle Windows After Heatmap](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/example_images/found_cars.jpg)

## Applying the Pipeline to Videos

At the end of [pipeline.ipynb](https://github.com/SealedSaint/CarND-Term1-P5/blob/master/pipeline.ipynb) is some simple code that processes videos using this vehicle-detection pipeline. My final video output can be found on YouTube [here](https://www.youtube.com/watch?v=cFNJqk6LXj4).
