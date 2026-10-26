---
layout: page-fullwidth

title: "Print Vision: AI-Powered 3D Print Failure Detection API"
subheadline: "Detect failed 3D prints from a single webcam image with AI"
teaser: "A webcam, a small classifier, and an OctoPrint plugin for catching failed 3D prints before they waste a spool"
header: no

comments: true
categories:
- machine learning
- 3d printing
- computer vision
- api
- rapidapi
tags:
- 3d printing
- print failure detection
- spaghetti detection
- machine learning
- computer vision
- AI
- RapidAPI
- OctoPrint
- ntfy
- 3d printer monitoring
---
<style>
@media (max-width: 600px) {
  table tr { display: block; }
  table td { display: block; width: 100%; }
}
</style>

<div align="center">
<a href="https://printsheriff.com">
<h4><strong>printsheriff.com</strong></h4>
Live demo, OctoPrint plugin and API docs
</a>
</div>

<br>

<div align="center">
<img src="https://rapidapi-prod-apis.s3.amazonaws.com/36f06624-ed5e-4caf-8675-1a23605558a8.png" alt="Print Vision" width="20%">
</div>

## Intro

A failed 3D print is expensive in the most boring way. You lose the filament, the printing time, and usually the printer keeps going as if nothing happened. If the printer sits in another room, you find the spaghetti hours later.

The camera is usually already there. A cheap webcam pointed at the bed sees enough to catch most failures early. The annoying part is what to do with the frames: streaming them to a server all day is wasteful on a Raspberry Pi setup, and I did not want a monitoring tool that stops working when the internet does.

So the project has two ways to run the same model:

* **Print Vision**, the HTTP API on RapidAPI. Send one image, get back a failure probability.
* **PrintSheriff**, an OctoPrint plugin that runs the model locally on the printer host.

Everything is on [printsheriff.com](https://printsheriff.com): the live demo, API docs, plugin install guide, attention gallery and technical details.

<div align="center" style="margin: 20px 0;">
  <iframe
    src="https://www.youtube.com/embed/LH1dH_dRXbo"
    width="650" height="650"
    style="border: none; border-radius: 12px;"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
  <br>
  <em>▶️ Demo video</em>
</div>

## Why not just use an LLM

Cost and latency. Sending every webcam frame to a multimodal model like GPT or Gemini is slow, and it gets expensive fast if you want to look at a frame every few seconds. A small classifier does the job here: one inference takes under 0.5 s, and the free tier on RapidAPI is enough to try it out.

## Print Vision API

The API is deliberately simple. It accepts the raw image bytes directly, so no JSON wrapper or multipart upload is needed.

```bash
curl --request POST \
  --url 'https://print-vision.p.rapidapi.com/v1/predict' \
  --header 'Content-Type: image/jpeg' \
  --header 'x-rapidapi-host: print-vision.p.rapidapi.com' \
  --header 'x-rapidapi-key: YOUR_RAPIDAPI_KEY' \
  --data-binary @print.jpg
```

And the response:

```json
{
  "failed_probability": 0.842317
}
```

A value close to `0` means the print is probably fine, close to `1` means it probably failed.

### Attention heatmaps

A number like `0.999997` is useful, but it still does not tell you *why* the model thinks the print failed. From the beginning I wanted a way to look at that, so the API can optionally return an attention heatmap.

```bash
curl --request POST \
  --url 'https://print-vision.p.rapidapi.com/v1/predict?include_attention=true' \
  --header 'Content-Type: image/jpeg' \
  --header 'x-rapidapi-host: print-vision.p.rapidapi.com' \
  --header 'x-rapidapi-key: YOUR_RAPIDAPI_KEY' \
  --data-binary @print.jpg
```

```json
{
  "failed_probability": 0.842317,
  "attention_jpeg_base64": "/9j/4AAQSkZJRgABAQAAAQ..."
}
```

The base64 JPEG is ready to decode and display.

I use this mostly when a prediction looks wrong. If the model is confident about a failure, the heatmap should light up the spaghetti, not the wall behind the printer or a random corner of the frame.

<table align="center">
  <tr>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/VvFN6Bv2/0j75lpgg9hwf1.jpg" alt="Original image" width="100%">
      <br>
      <em>Original image</em>
    </td>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/q7n4qXZR/0j75lpgg9hwf1-gradcam-unknown.jpg" alt="Neural network attention map" width="100%">
      <br>
      <em>Attention map, predicted failure probability: 1</em>
    </td>
  </tr>
</table>

This is also how I found several false positives during development. Sometimes the model was looking at the background instead of the print, which is much easier to notice with a heatmap than with a single number.

## printsheriff.com

Print Vision is the hosted API. The rest of the project lives at [printsheriff.com](https://printsheriff.com), and the plugin is what I actually use on my own printer.

The site has a [live demo](https://printsheriff.com/demo) where you can upload a photo or use your webcam, the [API docs](https://printsheriff.com/api), an [attention gallery](https://printsheriff.com/attention), and the [OctoPrint plugin](https://printsheriff.com/plugin) with install instructions. The plugin is the part I would recommend if you already run OctoPrint, because the frames never leave your local network.

### OctoPrint plugin

The plugin hooks into OctoPrint's timelapse capture event, so it scores one frame per capture instead of trying to process the whole video stream. Before inference the frame is cropped to a centred square and resized to 224x224. On a 640x480 webcam that keeps the middle 480x480 pixels and throws away 80 pixels from each side.

Inference runs locally with TFLite. No cloud service, no account, and detection keeps working when the internet is down.

<table align="center">
  <tr>
    <th align="left">Hardware</th>
    <th align="left">Inference time</th>
  </tr>
  <tr>
    <td>Raspberry Pi 5</td>
    <td>~40 ms</td>
  </tr>
  <tr>
    <td>Raspberry Pi Zero 2 W</td>
    <td>~480 ms</td>
  </tr>
</table>

Even on the Zero 2 W that is well within a normal timelapse interval, and it only does work when a frame is captured.

A single frame is noisy though. A nozzle passing through the shot can flip the score for a moment, so the plugin requires 3 of the last 5 frames to cross the threshold (0.8 by default) before it raises an alert. A single good frame in the middle of a failure does not reset it either.

Here is what it looks like in the OctoPrint panel:

<table align="center">
  <tr>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/t4H3Ctzh/detection-failed-small.png" alt="OctoPrint panel showing status FAILED with 88.5 percent" width="100%">
      <br>
      <em>Spaghetti detected, 88.5% failure score</em>
    </td>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/PrDbjF1z/detection-good.png" alt="OctoPrint panel showing status OK with 25.5 percent" width="100%">
      <br>
      <em>Normal print, 25.5% failure score</em>
    </td>
  </tr>
</table>

One thing that matters more than I expected: aim the camera so the print and the build plate are in the middle of the frame. The model only sees the centre crop, so a failure at the far edge of a wide shot is cropped away before inference. This was the most common cause of missed detections when I tested different camera setups.

### Alerts with ntfy

When a failure sequence triggers, the plugin sends one push per sequence, not one per frame, with the offending frame attached. Alerts go through [ntfy](https://ntfy.sh), which is free and does not need an account. You install the app, subscribe to a topic, and put the same topic in the plugin settings.

The plugin can also send a notification when a print finishes, which is nice for getting a snapshot of the part without opening OctoPrint.

<table align="center">
  <tr>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/MKB0Sr10/failed-ntfy.png" alt="ntfy failure alert with camera frame" width="70%">
      <br>
      <em>Failure alert</em>
    </td>
    <td align="center" width="50%">
      <img src="https://i.postimg.cc/RVt1Bbwd/finish-ntfy.png" alt="ntfy print finished alert with camera frame" width="70%">
      <br>
      <em>Print finished</em>
    </td>
  </tr>
</table>

### Install

The plugin is free and open source, no account or subscription. The current release is v0.1.0, so expect a few rough edges.

It can be installed from OctoPrint's Plugin Manager: **Settings > Plugin Manager > Get More > ...from URL**, then paste the release archive:

```text
https://github.com/xeonqq/octoprint_printsheriff/archive/refs/tags/0.1.0.zip
```

There are two short videos on the [how-to page](https://printsheriff.com/howto) if the written steps are not enough: one for the install and one for the ntfy setup.

### Help improve the model

The detector only gets better with images from printers it has not seen. Your camera angle, lighting and filament are exactly the kind of data the training set is missing.

In the plugin settings there is an option called **Help improve the model**. It is off by default. If you turn it on, the plugin uploads every 5th capture plus frames that land near the threshold, where the model is least sure. Only the cropped camera image is sent, no G-code or file names, and the images are deleted after 14 days.

If you get a false alarm or a missed failure, turning this on for a few prints is probably the most useful thing you can do for the next version. You can also send individual images through the contact form on the site.

## Training data

The model is trained on tens of thousands of crowdsourced 3D printing images, not on one controlled camera setup. The diversity matters more than the raw accuracy number.

A model trained on one printer in one room can look great on the validation set and then fail completely in someone else's workshop, because it learned the background instead of the print.

The test set is also held out by source, so frames from a printer seen during training do not show up in the evaluation set.

## ROC curve

The model returns a probability instead of a hard yes/no, so the threshold is a deployment decision. The ROC curve shows the trade-off on the held-out test set.

<div align="center">

<img src="https://i.postimg.cc/6pHxQb0q/roc-plot.png"
  alt="Print Vision ROC curve"
  width="80%">

</div>

For the plugin I kept the default threshold at 0.8, which favours fewer false alarms. A monitoring tool that cries wolf gets switched off after a week, so I would rather miss a borderline failure than wake someone up for a good print.

## Conclusion

The long-term goal is still simple:

> **Give every 3D printer a pair of eyes.**

If you run OctoPrint, the plugin is the easiest way to try it, and it is free. If you are building your own monitoring system, the API on RapidAPI is there for that.

<div align="center">

<a href="https://printsheriff.com">
<h3>Try it on printsheriff.com →</h3>
</a>

<a href="https://github.com/xeonqq/octoprint_printsheriff">
OctoPrint plugin on GitHub
</a>
&nbsp;|&nbsp;
<a href="https://rapidapi.com/xeonqq/api/print-vision">
Print Vision on RapidAPI
</a>

</div>
