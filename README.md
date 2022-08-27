# Multilabel classification handwritten Japanese character with YOLOv4
## Abstract
This is main repository for Japanese character recognition with YOLOv4.
This repository was utilized for my Final Project and Scientific Paper that has been submitted in IES 2022.
The main focus of this project and research are to identify the capability, characteristic and potential of YOLOv4 to tackle the problem of Handwritten character especially in terms of Japanese character which is increase the complexities. In addition, our truly concern is to identify how YOLOv4 be able to extract and generalize the main feature in comparation of ideal dataset(black and white) and non-ideal dataset(real data/online condition data).

## High-level overview
![pipeline](https://user-images.githubusercontent.com/54882818/186874022-7dce3ced-4cfa-4c74-93cb-002e3f52c3ef.jpg) </br> <b>Figure 1. Model building workflow</b> </br>

- In this research, we only utilizing hiragana characters which is consist of 46 different characters. 
- In ETLCDB Dataset, the hiragana characters is documented in ETL-8 and ETL-9. AFter the extraction and merging together into one dataset, the dataset will be splitted 
using cross validation, in this cases we utilizes KFold.
- After splitting the data into train and test set. The train set will be augmented with various methods such as dilation, erotion, resize and brightness, these methods will be combined to achieve arround 2000 datas. The main reason why only train set is augmented, we wanted to limit the feature distribution within the train set and test set. In addition, we wanted to differs the features combination inside the train and test set to avoid training bias.
- We split the training scenario into two, the first scenario are purely using main dataset(ETLCDB) which is ideal dataset, then the second scenario is to combine the main dataset with the real data samples. The purposes is to find the performance degradation and some underlying factors that affecting the performances.
- For the validation, we utilizes three different metrics which is F1-score and mAP accuracy as classification metrics and IOU accuracy for bounding boxes metrics. Thus we further breakdown these metrics into its variable which is True Positive(TP), False Positive(FP) and False Negative(FN) to provide deeper analysis.
- Further testing, we tested the model towards real condition. For example, brightness, lightning, distances, character sizes and camera condition are a few factors that affecting the performances. However, we suspect distance may the most crucial factors, thus we performing distance test by taking the images in different distance started arround 12cm to the object until 34cm.

![APPS interaction yolo](https://user-images.githubusercontent.com/54882818/186877786-b5c7144b-e7c6-4be7-9492-c14d3824d591.jpg) </br> <b>Figure 2. Services design</b> </br>

The first services are Android java based as a client, The client roles are to provide an interface, taking a picture, resize and compression which will further sent to the second services Python Flask-REST API based for model inference and rendering the bounding boxes.

#### Client Side Android Java based
- After taking the picture, the picture will be saved in external storage, the URI then further used to load it back again and convert the images into PNG.
- After converted to PNG, image will be resized and converted to base64 and sent to API via Retrofit.
- The responses from API especially for images will be decoded from base64 to bitmap and displayed.

#### Server side Python Flask based
- After the API receive the images, the API will decode the data to the images and convert it into RGB format.
- Before the images goes to the inference process, the images must be resized just exactly as the input shape of the model.
- AFter inference, the API will extract every object inside bounding box in result of multiple images and its labels.
- Every images will be converted into base64 and the label will be encoded with UTF-8 format and sent those back to the client.

## Low-level overview
### Extract Transform Load(ETL Process) and Augmentation
- The project and research utilizes ETL Character Database(ETLCDB) that can be found [here](http://etlcdb.db.aist.go.jp/). 
- The preprocess is done with Python 3.8 and the details can be found in this #1 [repo](https://github.com/Sekigahara/ETL-extractor-YOLOv4).
- The main concern in this parts are performing ETL Process the dataset into images and labels.
- After ETL Process, the data will be further augmented
- The result of ETL process and augmentation will be reformated to adjust the required training format of AlexeyAB YOLOv4 Darknet.
### API development (server)
- The API details and repo can be found #2 [here](https://github.com/Sekigahara/Japanese-character-recognition-YOLOv4-Flask-API).
- The YOLOv4 model is deployed with this API using Darkeras.
### Java Android (client)
- The Application details and repo can be found #3 [here](https://github.com/Sekigahara/Japanese-character-recognizers-Java-YOLOv4-based-apps).
- EVery images and detection results are obtained from this platform.
### Hiragana self-gather dataset for Object detection
- This is self-gathered dataset that also applied to the research and my final project to improve the accurateness of bounding boxes.
- The repositories and the data characteristic can be seen #4 [here](https://github.com/Sekigahara/Hiragana-from-scratch-datasets).
