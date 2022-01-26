# ImageSegmentation on Indian Traffic Dataset 

In this project My objective is to perform Semantic segmentation on Indian Traffic Data set. Original Paper I
used are
- [U-Net: Convolutional Networks for Biomedical Image Segmentation](https://arxiv.org/abs/1505.04597)
- [Attention-guided Chained Context Aggregation for Semantic Segmentation](https://arxiv.org/abs/2002.12041v1)

# Context Information 

Image segmentation is a dense prediction task, as we want to classify every pixel in the image as a corresponding
class of what it represents in the image. It has very interesting applications in computer vision
domains like Autonomous vehicles, Medical Image Analysis, Snap chat/ Instagram filters, and many more.
In Image Segmentation, we are interested in two main task
1. **What is in the image? (classification)**
2. **where in the image it is located? (localization)**


In this project, I have used the Indian Traffic dataset. The dataset consists of images obtained from a frontfacing
camera attached to a car. The car was driven around Hyderabad, Bangalore cities, and their outskirts.

The dataset consists of 41 different kinds of objects, and these objects further sub-grouped into 21 different
classes. I have used 4000 images in which 3200 images were used for training and 800 images were used for
testing. sample classes: Derivable, Non-Derivable, Living things, vehicles, roadside objects, sky, etc

# Models

## Transfer learning using U-Net

The architecture consists of a **contracting path** to capture context and a symmetric **expanding path** that enables precise localization. 

In the **contracting (Encoder)** part of the architecture I used a **pre-trained ResNet-34** classification
network trained on Image net dataset. In the Encoder part we down-sample the spatial resolution
of the image, and increasing feature maps, by doing this we will learn features that are highly discriminating
in nature which is important for the image segmentation task (classification).

In a **expanding path (decoder)**, we need to up-samples the feature so that we can generate the higher resolution images, I tried to stack the
discriminative feature (lower resolution) learned in encoder architecture ( localization ) with features
learned from the previous layer of the decoder to generate higher resolution image, and gradually recovered
the original resolution. 


![U-net](/out/U-net.png)


## Attention-guided Chained Context Aggregation (CANET)

I tried to implement other encoder-decoder-based architecture which improves
the segmentation task by **aggregating multiple-scale context information.**

Encoder part of the network is series of 4 dilated ResNet Convolution layers, in the first layer we decrease the size by 2, then by 4, and for the next 3 layers by 8. After Encoder I have implemented the **Context Aggregation Module (CAM)** which consists of context flow
and global flow.

**Global flow** uses the encoded feature learned from the backbone network to get a global
receptive field, which is really important to understand the overall global context of the image, this **reduced
the error of classification for similar objects**. Global features is obtained by applying global average pooling
on the shared encoded features.

Inputs of the context flow are shared features from the encoder network and
features we get from upper information flow. This flow **aggregates the information of different spatial scales**
by using convolution and pooling layers, this can **eliminate the aliasing effect and will increase the receptive
field.** 

In the end, CAM combines the features of various spatial scales learned from global flow and context
flow using the residual connection, this will further enhance the context information. Then input will pass
through the Feature selection module which assigns different attention weights to different parts of the
input.

Up-sampled features from feature selection module, which represents higher-level semantic features. initial
layer in encoder network represents lower level features. The decoder will combine the feature from the C1
layer from encoded with feature selection input and then apply series of up-sampling convolution to create
the final output which has the same resolution as input.

![CANET Architecture](/out/CANET1.png)
![CANET Architecture](/out/CANET2.png)


# Loss Function and Evalution Metric

## Loss Function : Dice Coefficient

![Dice Loss](/out/diceloss.png)

## Evalution : IOU (Intersection Over Union)

This competition is evaluated on the mean average precision at different intersection over union thresholds. The IoU of a proposed set of object pixels and a set of true object pixels is calculated as:

IoU(A,B)= (A∩B)/(A∪B)


## Results:

### U-net Result
![U-net Results](/out/U-net-results.png)

### CANET Result
![CA-net Results](/out/canet-results.png)


