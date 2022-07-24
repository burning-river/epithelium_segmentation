### Introduction

In microscopic cancer images, the morphology of epithelium (tissue that lines the outer surfaces of organs and blood vessles throughout the body) plays a crucial role in predicting overall survival and outcome in cancer patients. This makes segmentation of epithelium cells from stromal tissue a crucial step in  digital pathology. Manual segmentation of epithelia can be extremely cumbersome due to the sheer number of epithelial cells and their complex shapes (see example below). This is a problem well-suited to be solved by deep learning.

<p float="left">
<img src="https://github.com/sxk1031/digital_pathology/blob/main/images/12947_00004.jpg" width="250" height="250"/>
<img src="https://github.com/sxk1031/digital_pathology/blob/main/images/12947_00004_mask.png" width="250" height="250"/>
</p>
On the left is a raw ER+ BCa (estrogen receptor positive breat cancer) image. Dark purple regions are epithelium and pink+white regions are stroma. The image on the right is the corresponding mask with epithelial cells in white and stroma in black. The mask was a result of manual segmentation by an expert pathologist. Our job is to segment the epithelial regions automatically using deep learning.

### Dataset Description

The training data consists of 42 images (1,000 x 1,000) from the same number of patients. Each patient only has one image. 

### Methodology

#### Patch extraction

For each patient, let's extract 100 patches of size 500 x 500. Before extracting patches, let's pad the images with size 500 so that regions close to the edges can also be trained on. The number of patches is limited by the computing resources available. Patch size is more than twice than the input training size of patches (256 x 256) so that non-local regions of the bigger patch are cropped during augmentation. This is important because we want the segmentation network to learn from patches which don't look alike.    

#### Creating database

After extracting the patches from each whole slide image of a patient, we will store them in a pytable built on top of hdf5 library (hierarchical data format) and numpy package. It allows patches to be read and augmented in parallel by GPUs. Reading patches from pytables is much faster as compared to reading from images. This is especially important if one wants to read a patch a hundred epochs during training.

#### UNet training

We will use the highly successful UNet model for segmentations. See the [paper](https://arxiv.org/abs/1505.04597) for more details on the unet architecture. We will use cross-entopy for each pixel as the loss function.

#### Results

Below is an example of the performance of the unet on a validation image. The dice score is 0.78.
<img src="https://github.com/sxk1031/digital_pathology/blob/main/images/final_result.png" width="250" height="250"/>          
