# Lung-Cancer-Detection

The aim here is to take the sample data, consisting of low-dose CT scan information, and predict what the liklihood of a patient having lung cancer is. 

## Handling the Data:
The data consists of many 2D "slices," which, when combined, produce a 3-dimensional rendering of whatever was scanned. In this case, that's the chest cavity of the patient. We've got CT scans of about 1500 patients, and then we've got another file that contains the labels for this data.
Below is a sample CT scan slice: 

<img src="https://www.kaggleusercontent.com/kf/843901/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..3MBwdqYq6oMT3p55kEiKPw.xaTi9NJ2Rm9EEgQskBOWa-9bdXhGLp21cq3nhR2mT_WaUpYN5jHpUQSpvERl6JWewb6fBrhP_C-IF-hGr-4kR-Q1xAihLM1sl8bdGjVQePVxT5bEMOFZj43R19wnjU7pdmBnsr24jQ6y-rjAsGOyumrv_sdmCzRWWJKYnVBt8urPjRVSJgisqWwsGHstoEe3_AsUEgCAWsk-FmEvSbKjZBP4rCfVXlwK6c4iaW95832Bc74NS6cjIb33R7LW358TKFiwCVmm-P4j4NAMfR--5TbMy8cDeSCX1KT-DTWHnDlMvuFj74hhTL5RywQL3PpRWnYjjb76r-AXLCV9oVCQNcz_IpB3YqojneEyieklY9tyk4i0G_rosiy6e67assqIaWEvkeubyTXnUtx96oHYFSy4NwN7tRpJSrOPDOvhgVkn76mLNdGmUhAlf3UvbjuGWdI7zx00grKxgnskuzLuGkzHS1m73bY9jcA2ZqowVqkcimcws1CWvVOxO84PJwfqtvjhctT8vICaAh7Nt7xjjq7No0dP6UvbS6dGx4pVeZUbJs_vOO_VbH5JT5gmFZBb7-c8bcxBIFRGWeZkKfk1Q2K5K0RZqeJgavybZGh606zV_LT3qw8W-H7IB2rFN6d0RN4JVnzNxJFDByD3AzU-dNKYRiVfdCa7DDic0HTskgk.97JaITsQy2dOm8b-6FvAow/__results___files/__results___10_0.png">

## Processing and viewing our Data:

We're resizing our images from 512x512 to 150x150.

<img src="https://www.kaggleusercontent.com/kf/843901/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..3MBwdqYq6oMT3p55kEiKPw.xaTi9NJ2Rm9EEgQskBOWa-9bdXhGLp21cq3nhR2mT_WaUpYN5jHpUQSpvERl6JWewb6fBrhP_C-IF-hGr-4kR-Q1xAihLM1sl8bdGjVQePVxT5bEMOFZj43R19wnjU7pdmBnsr24jQ6y-rjAsGOyumrv_sdmCzRWWJKYnVBt8urPjRVSJgisqWwsGHstoEe3_AsUEgCAWsk-FmEvSbKjZBP4rCfVXlwK6c4iaW95832Bc74NS6cjIb33R7LW358TKFiwCVmm-P4j4NAMfR--5TbMy8cDeSCX1KT-DTWHnDlMvuFj74hhTL5RywQL3PpRWnYjjb76r-AXLCV9oVCQNcz_IpB3YqojneEyieklY9tyk4i0G_rosiy6e67assqIaWEvkeubyTXnUtx96oHYFSy4NwN7tRpJSrOPDOvhgVkn76mLNdGmUhAlf3UvbjuGWdI7zx00grKxgnskuzLuGkzHS1m73bY9jcA2ZqowVqkcimcws1CWvVOxO84PJwfqtvjhctT8vICaAh7Nt7xjjq7No0dP6UvbS6dGx4pVeZUbJs_vOO_VbH5JT5gmFZBb7-c8bcxBIFRGWeZkKfk1Q2K5K0RZqeJgavybZGh606zV_LT3qw8W-H7IB2rFN6d0RN4JVnzNxJFDByD3AzU-dNKYRiVfdCa7DDic0HTskgk.97JaITsQy2dOm8b-6FvAow/__results___files/__results___13_0.png"> 

One major issue is these colors and ranges of data. It's unclear to me whether or not a model would appreciate that. Even if we do a grayscale colormap in the imshow, we'll see that some scans are just darker overall than others. This might be problematic and we might need to actually normalize this dataset. We can now see our new data:

<img src="https://www.kaggleusercontent.com/kf/843901/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..3MBwdqYq6oMT3p55kEiKPw.xaTi9NJ2Rm9EEgQskBOWa-9bdXhGLp21cq3nhR2mT_WaUpYN5jHpUQSpvERl6JWewb6fBrhP_C-IF-hGr-4kR-Q1xAihLM1sl8bdGjVQePVxT5bEMOFZj43R19wnjU7pdmBnsr24jQ6y-rjAsGOyumrv_sdmCzRWWJKYnVBt8urPjRVSJgisqWwsGHstoEe3_AsUEgCAWsk-FmEvSbKjZBP4rCfVXlwK6c4iaW95832Bc74NS6cjIb33R7LW358TKFiwCVmm-P4j4NAMfR--5TbMy8cDeSCX1KT-DTWHnDlMvuFj74hhTL5RywQL3PpRWnYjjb76r-AXLCV9oVCQNcz_IpB3YqojneEyieklY9tyk4i0G_rosiy6e67assqIaWEvkeubyTXnUtx96oHYFSy4NwN7tRpJSrOPDOvhgVkn76mLNdGmUhAlf3UvbjuGWdI7zx00grKxgnskuzLuGkzHS1m73bY9jcA2ZqowVqkcimcws1CWvVOxO84PJwfqtvjhctT8vICaAh7Nt7xjjq7No0dP6UvbS6dGx4pVeZUbJs_vOO_VbH5JT5gmFZBb7-c8bcxBIFRGWeZkKfk1Q2K5K0RZqeJgavybZGh606zV_LT3qw8W-H7IB2rFN6d0RN4JVnzNxJFDByD3AzU-dNKYRiVfdCa7DDic0HTskgk.97JaITsQy2dOm8b-6FvAow/__results___files/__results___19_0.png">


## Preprocessing our Data:




## 3D Convolutional Neural Network:

We've got preprocessed, normalized, data. Now we're ready to feed it through our 3D convnet.







**Citation**

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
