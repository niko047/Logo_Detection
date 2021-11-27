# Logo Detection
A logo detection system using YOLOv5.

## Intro
Brands want to understand who uses their products and how. One way to do so is to use Computer Vision algorithms to detect relevant pictures on social media. Object Detection is the method of detecting objects in images or videos. 
Our goal here was to train a model able to detect brand logos.

## Detection Results
 -- put here some pictures with the bounding boxes --

## Table of contents
1. [ Environment ](#env)
2. [ Description ](#usage)
    1. [ Dataset ](#dataset)
    2. [ Training ](#train)
    3. [ Evaluation ](#eval)
3. [ Usage Tips ](#desc)
4. [ Usage Example ](#ex)

<a name="env"></a>
## 1. Environment
<div align="center">
    <a href="https://colab.research.google.com/github/ultralytics/yolov5/blob/master/tutorial.ipynb">
        <img src="https://github.com/ultralytics/yolov5/releases/download/v1.0/logo-colab-small.png" width="10%"/>
    </a>
    <a href="https://www.googleadservices.com/pagead/aclk?sa=L&ai=DChcSEwiS0f6Bt7j0AhWRzXcKHXXkA0gYABAAGgJlZg&ae=2&ohost=www.google.com&cid=CAESQOD2WXDDC3bcaN6__E7gY08J137qyTW6nOQb8DRsJPfVaCbKW_MnwwecmS8dCR7oZPQSLYd6V8LfB32ZLnpJUqA&sig=AOD64_2JGNrArPWvbnOJLMOXwqSsXl1gSw&q&adurl&ved=2ahUKEwjrovWBt7j0AhW3gv0HHXPGCYYQ0Qx6BAgCEAE&dct=1">
        <img src="https://aspiracloud.com/wp-content/uploads/2019/07/azure.png" width="10%"/>
    </a>
</div>

We developed our codes using *Google Colab*, and then we trained the largest ones on an *Azure Virtual Machine*:   


<p align="center">
<img src="https://github.com/Kasrazn97/Logo_Detection/blob/main/images/VM.jpeg" width="800" height="200">
<p>
 
<a name="desc"></a>
## 2. Description

We used two model architectures from the **YOLOv5** family : **YOLOv5s**, **YOLOv5l**. Experimenting with dataset preprocessing steps and fine-tuning procedures, we produced 5 different models and compared their performance. 

We chose [YoloV5](https://github.com/ultralytics/yolov5/blob/master/README.md) since it is state-of-the-art model and is considered to be one of the best in terms of speed and accuracy trade-off. Here the details of the two models we chose to train (you can find the details of all the YOLOv5 models [here](https://github.com/ultralytics/yolov5/blob/master/README.md)):
 
|Model |size<br><sup>(pixels) |mAP<sup>val<br>0.5:0.95 |mAP<sup>val<br>0.5 |Speed<br><sup>CPU b1<br>(ms) |Speed<br><sup>V100 b1<br>(ms) |Speed<br><sup>V100 b32<br>(ms) |params<br><sup>(M) |FLOPs<br><sup>@640 (B)
|---                    |---  |---    |---    |---    |---    |---    |---    |---
|YOLOv5s      |640  |37.2   |56.0   |98     |6.4    |0.9    |7.2    |16.5
|YOLOv5l      |640  |48.8   |67.2   |430    |10.1   |2.7    |46.5   |109.1
<br>
   
   
<a name="dataset"></a>
### 1. Dataset

The raw dataset we deployed consists of images representing the following logos: Nike, Adidas, Under Armour, Puma, The North Face, Starbucks, Apple Inc., Mercedes-Benz, NFL, Emirates, Coca-Cola, Chanel, Toyota, Pepsi, Hard Rock Cafè. We used [Roboflow](https://roboflow.com/?ref=ultralytics) to convert dataset to a COCO format and apply preprocessing steps which include image resize and data augmentation. In particular we applied the following augmentations: rotation, blur, flip, shear, exposure, mosaic, crop (both at image and bounding box levels). 
 
 <div align="center">
    <a href="https://roboflow.com/?ref=ultralytics">
        <img src="https://github.com/ultralytics/yolov5/releases/download/v1.0/logo-roboflow-long.png" width="35%"/>
    </a>
</div>

<div align="center">
 <img src="https://github.com/Kasrazn97/Logo_Detection/blob/main/images/NO_augmentation.png">  <img src="https://github.com/Kasrazn97/Logo_Detection/blob/main/images/augmentation.png">
</div>

<a name="train"></a>
### 2. Training

Our final models differ both in the input data used and the training steps applied.
 
1. **Yolov5s (version 1)**: trained on the raw dataset to which we added augmentations. We kept the 10 backbone layers frozen and fine-tuned the rest. Since the model results were unsatisfatory, we manually cleaned the data by removing the poorly annotated images. **TO DO: ADD TOT NUMBER OF IMAGES**
2. **Yolov5s (version 2)**: cleaned dataset with augmentations (about 20k images in total). Again, we trained all layers except for the backbone. **TODO: ADD NUMBER OF IMAGES PER LOGO AFTER THE CLEANING, BUT BEFORE AUGMENTATION**
3. **Yolov5s (version 3)**: cleaned dataset with extra augmentation steps, for a total of around 60k images (4549 per logo). Only 6 last layers were trained, thus keeping 18 frozen.
4. **Yolov5s (version 4)**: combined dataset from step 2 and step 3, with arounf 8994 images for each logo. Tuning all the layers except for the backbone.
5. **Yolov5l**: combined dataset from step 2 and step 3, adding more augmentation steps. We ended up having 7479 images for each logo. Again, we trained all the layers except for the backbone.

<a name="eval"></a>
### 3. Evaluation
We used 2 different metrics to evaluate our model:
  - **IoU**
  - **mAP**

**IoU**, Intersection over Union, is an evaluation metric used to evaluate the goodness of an object detector by measuring the overlap between two bounding boxes.
**mAP**, mean Average Precision, of the model measures the Average Precision (computed by calculating the AuC for a particular class) averaged over all the classes .

 Here the average results for each model: 
 
 <center>
  
| Model| mAP<sup>val<br>0.5 |mAP<sup>val<br>0.5:0.95 | IoU
| :-----: | :-: | :-: | :-:
| YOLOv5s - v1 |  |  |
| YOLOv5s - v2 |   |  |
| YOLOv5s - v3 |   |  |
| YOLOv5s - v4 | 0.881 | 0.664 |
| YOLOv5l | 0.943 | 0.713 |
<br>
 </center>
  
   Here the results for **YOLOv5l** for each logo: 
 
 <center>
  
| Logo| mAP<sup>val<br>0.5 |mAP<sup>val<br>0.5:0.95 | IoU
| :-----: | :-: | :-: | :-:
| Adidas | 0.98 | 0.753 |
| Apple Inc. | 0.981 | 0.761 |
| Chanel | 0.981 | 0.678 |
| Coca Cola | 0.886 | 0.619 |
| Emirates | 0.779 | 0.569 |
| Hard Rock Cafè | 0.957 | 0.743 |
| Mercedes Benz | 0.984 | 0.789 |
| NFL | 0.965 | 0.731 |
| Nike | 0.959 | 0.706 |
| Pepsi | 0.942 | 0.676 |
| Puma | 0.925 | 0.667 |
| Starbucks | 0.975 | 0.823 |
| The North Face | 0.975 | 0.741 |
| Toyota | 0.961 | 0.737 |
| Under Armour | 0.977 | 0.71 |
<br>
 </center>
 
<a name="usage"></a>
## 3. Usage Tips
 Instructions to follow - this file is needed for this; to test our algo run this
 
 <a name="ex"></a>
## 4. Usage Example
put the commands to run on the terminal to use our algo
