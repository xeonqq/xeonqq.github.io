---
layout: page
title: "Second Order System Transient Reponse with Python"
subheadline: "Control Theory"
teaser: ""
header: no
image:
    title: second_order_system.png
    thumb:  second_order_system.png
    homepage: second_order_system.png
    caption: second order system

comments: true
categories:
    - constrol theory
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


## Control Theory
When people discuss the tool to implement control theory, normally Matlab is of the first choice. However, as the moment of writting, we can achieve most of the Matlab 
functionality using Python. The libary we are going to use today is *scipy*, and more specifically *scipy.signal*. At the same time, we will understand more the second order system as well as its step response.

[Source code on github](https://github.com/xeonqq/control_theory.git)

## Second Order System
The system we are going to consider is the following servo system:

<img src="{{ site.urlimg }}servo_system.png" alt="servo system" style="width:100%">
The system consists of a proportional control and load elements (inertia and viscous friction elements). The control objective is to let the output position C track
the desired input position R

We will use *lti*, *dstep* from *scipy.signal*

```python
from scipy.signal import lti, dstep

T = 1
dt = 0.1
omega=1/T
zeta = 1
num = [omega**2]
den = [1, omega* 2*zeta, omega**2]
system = lti(num, den)
response_t, response = system.step(X0=0, T=ts)
```
<img src="{{ site.urlimg }}second_order_dynamic_different_damping_ratio.jpg" alt="different damping ratio" style="width:100%">
<img src="{{ site.urlimg }}step_response_seoncd_order_dynamic_critial_damp.png" alt="critial damping" style="width:100%">


{% if true %}
<div class="ads">
{% include advertising_wide.html %}
</div><!-- /.ads -->
{% endif %}

[14]: https://raw.githubusercontent.com/daviddao/spatial-transformer-tensorflow/master/data/mnist_sequence1_sample_5distortions5x5.npz
