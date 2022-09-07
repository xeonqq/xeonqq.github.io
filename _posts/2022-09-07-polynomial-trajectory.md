---
layout: page
title: "Autonomous Driving 101: Minimum Jerk Trajectory Generation with 5th Order Polynomial"
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
- *Pressing of gas pedal* (longitudinal control)
- *Steering of the Wheel* (lateral control)

## Let's consider the Longitudinal case first

Take *Pressing of gas pedal* for example, by pressing it "harder" or "softer", we are demanding a certain acceleration (2nd order derivative of distance) to the car. The rate of change of the gas pedal is the jerk that we are commanding. 
<iframe src="https://giphy.com/embed/l0HlRvbwQG5kWECSQ" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/southparkgifs-l0HlRvbwQG5kWECSQ">via GIPHY</a></p>
So intuitively if you press the pedal too quick and release it abruptly, it can easily results in physical discomfort.

So as for autonoumous vehicle, it would make sense to generate a trajectory, which can go from start position to goal position, and at the same time tries to minimize the jerk introduced during the execuation of the trajectory.

So if we represent distance $x$ as a function of time $x=f(t)$, jerk will be the 3rd derivative of it, so $j=f^{(3)}(t)$, or simply $j=x'''$ for short.

## Problem definition

We want to generate a 1D trajectory, which goes from $x_0$ to $x_1$ longitudinally, in given time from $t_0$ to $t_1$

In addition, the generated trajectory has minimal jerk, namely the sum of $x'''$ squared is minimal.

other minimal snap or even higher order trajectory follow the same principle

Calculus of Variations
Coursera course [Robotics: Aerial Robotics](https://www.coursera.org/learn/robotics-flight/lecture/oRtvH/time-motion-and-trajectories) from Prof. Vijay Kumar gives a quick introduction of 

Reference from Wikipedia:
Euler Lagrange Equation
<img src="{{ site.urlimg }}euler-lagrange-equation.png" alt="euler-lagrange-equation" style="width:100%" class="center">

   <img src="{{ site.urlimg }}minimum_jerk_trajectory.jpg" alt="minimum_jerk_trajectory" style="width:100%" class="center">



