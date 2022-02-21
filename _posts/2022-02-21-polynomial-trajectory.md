---
layout: page
title: "Minimum Jerk Trajectory Generation with 5th Order Polynomial"
subheadline: "trajectory planning"
teaser: "Do you wonder how autonoumous vehicle generate same and comfortable trajectory, polynomial trajectory planning is one way"
header: no
image:
    title: header_planning.png
    thumb:  header_planning.png
    homepage: header_planning.png
    caption: trajectory planning 
    caption_url: "https://miro.medium.com/max/1108/1*ufWDxL-5ogd22Rg_37rakw.png"

comments: true
categories:
    - trajectory planning
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

### Intro
Trajectory planning applies to all moving robot, be it an automous vehicle, or a flying quadcopter. Polynomial based approach is one of the solutions to this problem. 
Many tutorials online only teaches the *HOW* by demonstrating the solution. On the other hand, in this post, we will dive into the *WHY*, and invetiably face some challenging math (*where the real fun lies*).

In this post, the author will try to answer the following questions:
 - What does minimum jerk trajectory mean and why do we need it?
 - Why 5th order polynomial is needed for minimum jerk trajectory?
 - How to derive the coeffients for the 5th order trajectory

Moreover, after understanding minimum jerk trajectory, it should be trivial to derive minimum acceleration (3nd order poly) or minimun snap (7nd order poly) for yourself.

### Minimum Jerk Trajectory

Consider the car that we humans are driving, what is the controll input we give to the system?
- *Pressing of Gas pendel* 
- *Steering of the Wheel*

Take *Pressing of Gas pendel* for example, by pressing it "harder" or "softer", we are demanding a certain acceleration (2nd order derivative of distance) to the car. And due to inertia of the car, it is never physically possible to demand an arbitary velocity (1st oder derivative of distance) to the car, so the velocity can not have sudden jump. Therefore it can be defined that car is a *2nd order system*.

- *Steering of the Wheel*
Calculus of Variations
Coursera course [Robotics: Aerial Robotics](https://www.coursera.org/learn/robotics-flight/lecture/oRtvH/time-motion-and-trajectories) from Prof. Vijay Kumar gives a quick introduction of 

Reference from Wikipedia:
Euler Lagrange Equation
<img src="{{ site.urlimg }}euler-lagrange-equation.png" alt="euler-lagrange-equation" style="width:100%" class="center">

   <img src="{{ site.urlimg }}minimum_jerk_trajectory.jpg" alt="minimum_jerk_trajectory" style="width:100%" class="center">

## Problem definition

We want to generate a 1D trajectory, which goes from $x_0$ to $x_1$, in given time $T$.

In addition, the generated trajectory has minimal jerk, namely the sum of $x'''$ squared is minimal.

other minimal snap or even higher order trajectory follow the same principle


{% if true %}
<div class="ads">
{% include advertising_wide.html %}
</div><!-- /.ads -->
