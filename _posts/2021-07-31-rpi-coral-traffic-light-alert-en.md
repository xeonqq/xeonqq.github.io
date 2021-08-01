---
layout: page
title: "Co-Pilot, A Machine Learning Based Real-Time Traffic Light Alert on Your Car with Raspberrypi and Coral"
subheadline: "Machine Learning"
teaser: "Imaging when you there is a co-pilot in your car which alerts you the traffic light ahead of you. Be it an alert of red light, or
a reminder to press gas when it turns green, and more."
header: no
image:
    title: traffic_light_detection_sample.png
    thumb:  traffic_light_detection_sample.png
    homepage: traffic_light_detection_sample.png
    caption: traffic light detection in action

comments: true
categories:
    - machine learning
---
<style>
* {
  box-sizing: border-box;
}

.img-container {
  float: left;
  width: 9.09%;
  padding: 5px;
}
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
.clearfix::after {
  content: "";
  clear: both;
  display: table;
}
.caption {
  font-size: 12px;
  text-align: center;
}
</style>

`Co-Pilot` = Raspberrypi 3/4 + rpi camera + Google Coral TPU = Machine learning based real-time 
traffic light alert and dash cam in your car. 
Voice alert supports English/中文.

[Source code on github](https://github.com/xeonqq/co-pilot.git)

`Co-Pilot` can be mounted directly in the wind shield, with the camera facing front. It can be powered by any 5V USB port in car.

It not only serves
as a dashcam which records the camera feed, it also alerts the driver in real time about incoming traffic light status.
It acts like a co-pilot which can help inattentive driver avoid violating traffic rules.
 
## Watch the demo in car
<iframe width="720" height="512" src="https://www.youtube.com/embed/tCmUoWLdjoo" frameborder="0" allowfullscreen></iframe>


## What `Co-Pilot` sees
<img src="https://github.com/xeonqq/co-pilot/images/traffic_light_detection_seq.gif" alt="" style="width:100%">

## Hardware Setup
![](https://github.com/xeonqq/co-pilot/images/hardware.jpg)
* Raspberry Pi 3
* Raspberry Pi Camera v2
* [Raspberry Pi 3 case](https://www.berrybase.de/raspberry-pi/raspberry-pi-computer/gehaeuse/fuer-raspberry-pi-3-2-1-modell-b/acryl-case-f-252-r-big7-und-raspberry-pi)
* [Google Coral TPU Accelerator](https://coral.ai/products/accelerator)
* [3.5mm audio cable](https://www.berrybase.de/audio-video/kabel-adapter/klinke/klinken-audiokabel-stereo-1x-3-5mm-klinkenstecker-150-1x-3-5mm-klinkenstecker-schwarz)
* JBL GO Speaker
* GoPro mount and 3M tape
* optional: [RTC DS3231](https://www.berrybase.de/raspberry-pi/raspberry-pi-computer/gpio-hats-phats/eingabe/ds3231-real-time-clock-modul-f-252-r-raspberry-pi) (For recording with real timestamp)
* optional: USB pendrive (For bigger storage of recordings)

## How it works
Once Co-Pilot is started, the Pi camera will continuously capture video at 1120x624 at 24fps, images will be captured during recording via
the video port at 5Hz. The image is then resized to 600x300 as the input for detecting traffic light and classifying its state.

For achieving this, two neural networks are used, a detection net to extract the locations of the traffic lights in the image and a classification net for classifying their states.
For detection I used a off-the-shelf [pre-trained SSD model](https://github.com/google-coral/test_data/blob/master/ssd_mobilenet_v2_coco_quant_postprocess_edgetpu.tflite), since it is already in 
the format of edgetpu tflite, it directly runs on Coral accelerator. The input size of the SSD model is only 300x300, therefore the detection net needs to be
applied twice to cover the whole image size. With the help Coral it takes ~150ms for the two inferences to complete. However, if we were running the 
 inference purely on CPU it would take 2.3 sec, which is not acceptable for real-time application.
 
Given the bounding box locations of the detected traffic lights, they are resized to 32x16 and then feed into a custom trained classification net. Its output consists of
the probabilities of 11 categories, namely \[*green, red, yellow, red_yellow, green_left, red_left, green_right, red_right, pedestrian, side, none*\].
<div class="clearfix">
  <div class="img-container">
  <img src="{{ site.urlimg }}lights/green.bmp" alt="" style="width:100%">
  <p class="caption">green</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/red.bmp" alt="" style="width:100%">
  <p class="caption">red</p>  
  </div>

  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/yellow.bmp" alt="" style="width:100%">
  <p class="caption">yellow</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/red_yellow.bmp" alt="" style="width:100%">
  <p class="caption">red_yellow</p>  
  </div>
  
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/green_left.bmp" alt="" style="width:100%">
  <p class="caption">green_left</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/red_left.bmp" alt="" style="width:100%">
  <p class="caption">red_left</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/green_right.bmp" alt="" style="width:100%">
  <p class="caption">green_right</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/red_right.bmp" alt="" style="width:100%">
  <p class="caption">red_right</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/pedestrian.bmp" alt="" style="width:100%">
  <p class="caption">pedestrian</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/side.bmp" alt="" style="width:100%">
  <p class="caption">side</p>  
  </div>
  <div class="img-container">
  <img src="{{ site.urlimg }}/lights/none.bmp" alt="" style="width:100%">
  <p class="caption">none</p>  
  </div>
</div>


The classification
net is a light weighted CNN which has similar architecture like LeNet. The classification net also runs on Coral TPU.

In the end, a tracker is implemented using Kalman filter and Hungarian algorithm to keep track only the relevant traffic light for the driver.
Certain pre-recorded voice alert will be played accordingly based on the state of the relevant traffic light.

{% if true %}
<div class="ads">
{% include advertising_wide.html %}
</div><!-- /.ads -->
{% endif %}


## Limitations
* Currently works only with vertically placed traffic lights, optimized for Germany.
* Delay of ~0.3 sec for each detection (Rpi 4 might have better performance, didn't have one to test)
* Can not see traffic light in far range, since the resolution is scaled down to save computation.
* Still some false positive detections, largely due to inaccurate and unstable bounding box detection from the pre-trained model. (There is a plan to retrain a new model)
* ~100ms delay on capturing RGB image, ~100ms delay on transferring data to Coral accelerator. Both problem might be mitigated
when depolyed on Pi 4 due to faster processing power and USB 3.0, which can enable higher frame rate.

# Deployment Instructions
## Dependencies
```
#on rpi
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
apt-get update
apt-get install -y libedgetpu1-std
apt-get install -y python3-pycoral
apt-get install -y python3-tflite-runtime
python3 -m pip install -r requirements_pi.txt
sudo apt-get install libsdl2-mixer-2.0-0  libsdl2-2.0-0
```
## Run Co-Pilot
```
git clone https://github.com/xeonqq/co-pilot.git
cd co-pilot
python3 -m src.main  --ssd_model models/ssd_mobilenet_v2_coco_quant_no_nms_edgetpu.tflite  --label models/coco_labels.txt --score_threshold 0.3 --traffic_light_classification_model models/traffic_light_edgetpu.tflite  --traffic_light_label models/traffic_light_labels.txt --blackbox_path=./
```
I use [superviser](http://supervisord.org/) to start co-pilot at RPI boot up.

## Adjust volume
Once you've SSH'd into your Pi, type "alsamixer". This will bring up an interface within the terminal which will allow you to set the volume of the Raspberry Pi. Simply press the up and down arrow keys to either increase or decrease the volume. When you are done, press ESC.

## Test
```
# under repo root folder
python3 -m pytest
# or
python3 -m tests.test_detection
python3 -m tests.test_classification
```

## Reprocess with recorded video (On Host PC)

Build and run docker container
```
./build.sh
./linux_run.sh
```

In docker container
```
cd workspace
python3 -m src.reprocess  --ssd_model models/ssd_mobilenet_v2_coco_quant_no_nms_edgetpu.tflite  --label models/coco_labels.txt --score_threshold 0.3 --traffic_light_classification_model models/traffic_light_edgetpu.tflite  --traffic_light_label models/traffic_light_labels.txt --blackbox_path=./ --video recording_20210417-090028.h264.mp4 --fps 5
```

Both main and reprocess can be run without Coral TPU by specifying --cpu option.

[14]: https://raw.githubusercontent.com/daviddao/spatial-transformer-tensorflow/master/data/mnist_sequence1_sample_5distortions5x5.npz
