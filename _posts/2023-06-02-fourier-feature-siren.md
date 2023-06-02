---
layout: page-fullwidth

title: "Fourier Feature Network(FFN) VS Sinusoidal Representation Networks(Siren)."
subheadline: "Machine Learning"
teaser: "Moreover, can we combine them?"
header: no

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
  width: 50%;
  padding: 5px;
  border: 1px solid black
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
</style>

<div align="center">
<a href="https://linkedin.com/in/xeonqq/">
<h4><strong>Qian Qian</strong></h4>
 Amateur researcher<br>
 </a>
</div>
<br>
<div align="center">
<a href="https://github.com/xeonqq/FourierFeatureSiren">
<img src="{{ site.urlimg }}github.png" alt="Code" class=center width="8%">
<h5><strong>Code</strong></h5>
</a>
</div>





### Abstract
Many recent work have been conducted in using multi-layer perceptron (MLP) as implicit data represenation. The underlying data to be represenated can be image, 3d structure, analog signals, etc.
However, so far there is no quantitative comparision between different methods. 

In this blog post, we will compare the performace of [Fourier Feature Network(FFN)](https://bmild.github.io/fourfeat/) and Siren MLP, proposed in work [Implicit Neural Activations with Periodic Activation Functions](https://vsitzmann.github.io/siren). In terms of accuracy in image fitting. 

In the end, we proposed a combined version **FourierFeatureSiren (FFSiren)**, which brings the advantages of both networks together. We show that it outperforms both existing models.

* The Siren implementation is a copy-paste from the original implementation from the author.

* FFN implementation is a pytorch port from the original jax implemenation from the author.

### Image Fitting Comparision
*Task*: Given the pixel coordinate (x,y) of the input camera_man (grayscale 256x256) image, predict the corresponding intensity of that pixel.

For fair comparision, all three models are having ~0.263 Million parameters, training with 500 steps and Adam optimizer with learning rate 1e-4.

<video width="100%" controls autoplay loop>
      <source src="{{ site.urlimg }}ffsiren.mp4" type="video/mp4">
</video>

### FourierFeatureSiren

FourierFeatureSiren(FFSiren) is simply a combined version of the two. It takes the input embbeding using fourier feature mapping, and feed it into a Siren network with sinusoidal activation functions
In terms of image fitting, the outcome of FFSiren out-performs both exisiting networks, its MSE loss is ~20 times less than FFN, yet still has the fast converge rate during training and low output noise like Siren.


### Results

|   |PSNR  | Pixel MSE  |  Gradient MSE |
|---|---|---|---|
|Siren   | 36.80  |0.0008   |  33.28 |
|FFN   | 38.43  | 0.00057  |  48.12 |
|**FFSiren**  | **51.83**  |**0.0000262**   | **18.12** |

- *PSNR: higher the better; MSE: lower the better*

<br>

<div class="row">
  <div class="large-6 columns" align="center">
   <h6><strong>Peak signal-to-noise ratio: </strong></h6>
  </div>
  <div class="large-6 columns" align="center">
  
   <h6><strong>Mean squared error: </strong></h6>
  </div>
</div>

<div class="row">
  <div class="large-6 columns" align="center">
      <img src="{{ site.urlimg }}psnr.png">
  </div>
  <div class="large-6 columns" align="center">
      <img src="{{ site.urlimg }}mse_loss.png">
  </div>
</div>

<br>
<div align="center">
   <h6><strong>Comparision on applying Sobel kernel on output images</strong></h6>
<img src="{{ site.urlimg }}gradient_comp.png" alt="gradient comparision" class=center width="100%">
</div>

<br>


### Conclusions

|   |Pros  | Cons  |  
|---|---|---|
|**Siren**   | Converges very fast during training, it has smooth output as well as its gradient and laplacian |The output image is not sharp enough, high frequency signals are not recovered)  | 
|**Fourier Feature Network (FFN)**  | Has higher PSNR in the end than Siren, The image is sharper than Siren  |Converges the slowest during training, it also induce significantly more noise in the final image|  
|**Fourier Feature Siren (FFSiren)**  |Converges as fast as Siren during training, it has the highest PSNR, and has much less noise than FFN| Training loss has slight sparks which seems big in PSNR plot (due to log operation)  | 
