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
