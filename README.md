# Tensorflow2_Custom-Object-Detection

* Step1 : Prepare Dataset
* Step2 : Labelling/Annotation
* Step3 : Train
* Step4 : Test

## Prepearing Dataset
First, you need to define what object you want to detect. In this example i have used "racoon" images to train.[https://public.roboflow.com/object-detection/raccoon]
Split the images into train and validation. 
  * Training images in "images/train". 
  * Validation images in "images/val".

## Labelling/Annotation
The next step is to Label/annotate the dataset according to respective class. In this case there is only one class : "raccoon".
I used labelImg from https://github.com/tzutalin/labelImg. It is a graphical image annotation tool written in Python and it uses Qt for its graphical interface.

![labelimg](https://user-images.githubusercontent.com/20577227/114542661-259b6a80-9c93-11eb-8e3c-f7d3c53fe7c8.JPG)

I have used PASCAL VOC format since it is supported by Tensorflow.

## Train
#### First
In TensorFlow label map is required, it maps each of the labels to an integer values. It is used in both training and detection processes.
The label map file is located in "data/label_map.pbtxt"
```
item {
  id: 1
  name: 'raccoon'
  display_name:'raccoon'
}
```
If you have more than 1 classes than label_map.pbtxt will like this
```
item {
  id: 1
  name: 'raccoon'
  display_name:'raccoon'
}
item {
  id: 2
  name: 'cat'
  display_name:'cat'
}
.
.
.
```
#### Second: Convert the Pascal VOC to TFRecord
**Set path of your current directory, slim directory**
**Edit setpath.bat according to your directory**

The TFRecord format is a simple format for storing a sequence of binary records. 
1. To do this we will first convert the XML file to CSV and than we will convert the "train.csv" and "val.csv" to TFRecord.


```
python FromVOC_to_csv.py
```
This will save the "train_labes.csv" and "val_labes.csv" to data folder

2. Create TFrecord

For Train TFRecord
```
 python Create_tfrecord.py --csv_location=data/train_labels.csv --output_location=data/train.record --image_dir=images/train
```
For Validation TFRecord
```
python Create_tfrecord.py --csv_location=data/val_labels.csv --output_location=data/test.record --image_dir=images/val
```

#### Third: Train
**Download "Faster R-CNN ResNet50 V1 640x640" pretrained model from "https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md" in your current directory**
**Copy "faster_rcnn_resnet50_v1_640x640_coco17.config" from "object_detection\configs\tf2" to current directory**

Edit "faster_rcnn_resnet50_v1_640x640_coco17.config" 
* Line 10 **num_classes: 1** according to your class.
* Line 97 **num_steps: 100000** How much step you want to train
* Line 113 **fine_tune_checkpoint: your_directory/faster_rcnn_resnet50_v1_640x640_coco17/checkpoint/ckpt-0"
* Line 126 **label_map_path: "your_directory/annotations/label_map.pbtxt"**
* Line 128 **input_path: "train.record"** Path to train.record file
* Line 139 **label_map_path: "your_directory/annotations/label_map.pbtxt"**
* Line 143 **input_path: "val.record"** Path to val.record file

Create "train_result" folder in the directory.

To train use the following 
```
python object_detection/model_main_tf2.py --pipeline_config_path=faster_rcnn_resnet50_v1_640x640_coco17.config --model_dir=train_result --alsologtostderr
```

#### Fourth : Test
Create "output_inference_graph" folder in current directory

To convert the trained checkpoint to saved model used the following script
```
python object_detection/exporter_main_v2.py --trained_checkpoint_dir train_result --pipeline_config_path faster_rcnn_resnet50_v1_640x640_coco17.config --output_directory output_inference_graph
```

