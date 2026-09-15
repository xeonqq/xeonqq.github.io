---
layout: page-fullwidth

title: "Print Vision: AI-Powered 3D Print Failure Detection API"
subheadline: "Detect failed 3D prints from a single webcam image with AI"
teaser: "A lightweight computer vision API for detecting spaghetti and other 3D printing failures"
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
- 3d printer monitoring
---
<style>
@media (max-width: 600px) {
  table tr { display: block; }
  table td { display: block; width: 100%; }
}
</style>

<div align="center">
<a href="https://rapidapi.com/xeonqq/api/print-vision">
<h4><strong>Try Print Vision on RapidAPI for Free</strong></h4>
3D print failure detection powered by machine learning
</a>
</div>

<br>

<div align="center">
<img src="https://rapidapi-prod-apis.s3.amazonaws.com/36f06624-ed5e-4caf-8675-1a23605558a8.png" alt="Print Vision" width="20%">
</div>

## Introduction

A failed 3D print can waste hours of printing time and a significant amount of filament.

A webcam pointed at a 3D printer can already provide everything needed to detect many common failures — but continuously processing camera images locally is not always practical, especially for small Raspberry Pi-based printer monitoring systems.

To make this easier, I developed **Print Vision**, an HTTP API that can determine whether a 3D printer is currently producing a good print or a failed print from a **single camera image**.

The API accepts a JPEG, PNG, or WEBP image and returns a probability that the print has failed.

It can also optionally return an **attention heatmap**, allowing you to see which part of the image influenced the model's prediction.
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

---

## Why not use LLM like GPT and Gemini
Costly and slow. 

Print Vision API: one inference take under 0.5 sec and is **free**

---

# Print Vision API

Print Vision is designed to make this functionality accessible through a simple HTTP API.

The API accepts the **raw image bytes** directly, so no JSON image wrapper or multipart upload is required.

For example:

```bash
curl --request POST \
  --url 'https://print-vision.p.rapidapi.com/v1/predict' \
  --header 'Content-Type: image/jpeg' \
  --header 'x-rapidapi-host: print-vision.p.rapidapi.com' \
  --header 'x-rapidapi-key: YOUR_RAPIDAPI_KEY' \
  --data-binary @print.jpg
```

The response is simple:

```json
{
  "failed_probability": 0.842317
}
```

A value close to `0` indicates a likely successful print, while a value close to `1` indicates a likely failed print.

## Attention heatmaps

One of the features I wanted to include from the beginning was a way to inspect **why** the model made a prediction.

A probability such as:

```json
{
  "failed_probability": 0.999997
}
```

is useful, but it is still a black-box prediction.

Print Vision can therefore optionally return an attention heatmap:

```bash
curl --request POST \
  --url 'https://print-vision.p.rapidapi.com/v1/predict?include_attention=true' \
  --header 'Content-Type: image/jpeg' \
  --header 'x-rapidapi-host: print-vision.p.rapidapi.com' \
  --header 'x-rapidapi-key: YOUR_RAPIDAPI_KEY' \
  --data-binary @print.jpg
```

The response contains:

```json
{
  "failed_probability": 0.842317,
  "attention_jpeg_base64": "/9j/4AAQSkZJRgABAQAAAQ..."
}
```

The base64-encoded JPEG can be directly decoded and displayed.

*Attention heatmap example. The highlighted regions indicate the areas that contributed most strongly to the prediction.*

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
      <em>Attention map of the neural network, predicted failure probability: 1</em>
    </td>
  </tr>
</table>


This is particularly useful when investigating **false positives and false negatives** during development.

It also makes it possible to sanity-check whether the model is actually looking at the print rather than relying on an accidental correlation such as the background, camera position, or lighting.

---

# Training data

The model is trained using **tens of thousounds of crowdsourced 3D printing images** rather than relying exclusively on images captured under one controlled laboratory setup.

The diversity of the data is important.

A model trained entirely on images from one printer in one room can achieve excellent validation accuracy while performing poorly when deployed somewhere else.

The goal is therefore not simply to achieve a high training accuracy, but to build a model that can **generalize to real-world printer monitoring environments**.

---


# Model performance ROC curve

The ROC curve is particularly useful because the probability returned by the model can be converted into different decision thresholds.

*ROC curve evaluated on the held-out test set.*
<div align="center">

<img src="https://i.postimg.cc/6pHxQb0q/roc-plot.png"
  alt="Print Vision ROC curve"
  width="80%">

</div>

The production threshold can then be chosen according to the intended application.

---

# Conclusion

The long-term goal is simple:

> **Give every 3D printer a pair of eyes.**

If you are building a 3D printer monitoring system, Raspberry Pi project, OctoPrint integration, or another computer-vision application, you can try the API through RapidAPI.

<div align="center">

<a href="https://rapidapi.com/xeonqq/api/print-vision">
<h3>Try Print Vision on RapidAPI →</h3>
</a>

</div>

