# Lung-Cancer-Detection

The aim here is to take the sample data, consisting of low-dose CT scan information, and predict what the liklihood of a patient having lung cancer is. 

## Handling the Data:
The data consists of many 2D "slices," which, when combined, produce a 3-dimensional rendering of whatever was scanned. In this case, that's the chest cavity of the patient. We've got CT scans of about 1500 patients, and then we've got another file that contains the labels for this data.
Below is a sample CT scan slice: 

<a href="https://imgbb.com/"><img src="https://i.ibb.co/c1KR7rf/1.png" alt="1" border="0"></a>

## Processing and viewing our Data:

We're resizing our images from 512x512 to 150x150.

<a href="https://imgbb.com/"><img src="https://i.ibb.co/tPNxrZN/2.png" alt="2" border="0"></a>

One major issue is these colors and ranges of data. It's unclear to me whether or not a model would appreciate that. Even if we do a grayscale colormap in the imshow, we'll see that some scans are just darker overall than others. This might be problematic and we might need to actually normalize this dataset. We can now see our new data:

<a href="https://imgbb.com/"><img src="https://i.ibb.co/y5Yjc2z/3.png" alt="3" border="0"></a>

## Resampling:

A scan may have a pixel spacing of [2.5, 0.5, 0.5], which means that the distance between slices is 2.5 millimeters. For a different scan this may be [1.5, 0.725, 0.725], this can be problematic for automatic analysis. 

A common method of dealing with this is resampling the full dataset to a certain isotropic resolution. If we choose to resample everything to 1mm1mm1mm pixels we can use 3D convnets without worrying about learning zoom/slice thickness invariance.

## 3D plotting the scan:

For visualization it is useful to be able to show a 3D image of the scan.

Our plot function takes a threshold argument which we can use to plot certain structures, such as all tissue or only the bones. 400 is a good threshold for showing the bones only.

<a href="https://ibb.co/1YNDQvs"><img src="https://i.ibb.co/gb859jv/with-air.jpg" alt="with-air" border="0"></a>

## Lung segmentation:
In order to reduce the problem space, we can segment the lungs. 
The steps:

Threshold the image (-320 HU is a good threshold, but it doesn't matter much for this approach)
Do connected components, determine label of air around person, fill this with 1s in the binary image
Optionally: For every axial slice in the scan, determine the largest solid connected component (the body+air around the person), and set others to 0. This fills the structures in the lungs in the mask.
Keep only the largest air pocket (the human body has other pockets of air here and there).




<a href="https://imgbb.com/"><img src="https://i.ibb.co/4sMC4m7/air.jpg" alt="air" border="0"></a>



## 3D Convolutional Neural Network:

We've got preprocessed, normalized, data. Now we're ready to feed it through our 3D convnet.
The number 54080 comes from this calculation: 
There are two maxpooling layers with strides=2 along each axis. These strides reduce the size of the 3d image from 50x50x20 to 13x13x5 (ceil(50/2/2) and ceil(20/2/2)). This reduced size image is what we call a feature map. There are 64 such feature maps right before the dense layer. Therefore, we must map all these feature maps to the dense layer (for feedforward operations) resulting in 13x13x5x64 = 54080 neurons.







### **Citation**

```
@article{,
title= {Data Science Bowl 2017 Lung Cancer Detection (DSB3) },
keywords= {},
author= {},
abstract= {In this dataset, you are given over a thousand low-dose CT images from high-risk patients in DICOM format. Each image contains a series with multiple axial slices of the chest cavity. Each image has a variable number of 2D slices, which can vary based on the machine taking the scan and patient.

The DICOM files have a header that contains the necessary information about the patient id, as well as scan parameters such as the slice thickness.

stage1.7z: 285380 dcm files
stage2.7z: 186160 dcm files
stage1_labels.csv: 1595 labels

},
terms= {},
license= {},
superseded= {},
url= {https://www.kaggle.com/c/data-science-bowl-2017/data}
}

```
