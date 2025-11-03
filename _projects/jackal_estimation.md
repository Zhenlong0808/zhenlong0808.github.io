---
layout: page
title: Filter-Based Estimation for Clearpath Jackal UGV
permalink: /projects/jackal-estimation/
description: Extended Kalman filter on Rugged Terrain and Indoor Localization with Particle Filter, with ROS 2/Gazebo.
img: /assets/img/projects/jackal/cover.jpg 
importance: 1
category: projects
mathjax: true 
---

---

## Extended Kalman filter on Rugged Terrain

This part implement an EKF for rugged, sloped terrain on the Clearpath Jackal UGV. The process model performs inertial propagation, integrating IMU-measured linear and angular rates to predict the vehicle’s motion between measurement updates. Process noise is modeled on the throttle input rather than the state because real uncertainty arises from actuator and terrain variability, which propagate through the system dynamics to the state via the analytical control Jacobian. 
$$
Q_k=G(x_k,u_k)\,\Sigma_u\,G(x_k,u_k)^\top,
$$
The set of states includes base position and quaternion. 

For small-area motion without reliable WGS-84, the measurement model GPS assume a locally flat Earth and build a data-driven planar frame by sampling a few ground-truth points in simulation and locally linear affine fit from (lat, lon, alt) to local ENU (x, y, z).

After a brief exploratory drive over uneven grade, the mean position error quickly falls below $$\|e\|_2<0.1\,\text{m}$$ despite modeling and measurement mismatch.

<video controls preload="metadata" width="100%" playsinline poster="/assets/img/projects/jackal/1-poster.jpg">
  <source src="/assets/video/projects/jackal/1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

---

## Indoor Localization with Particle Filter
This part implement a Particle Filter for indoor localization in the JHU Wyman Building (1F)
**Modeling & sensors.**  
The PF maintains $$\{(s_t^{(i)},w_t^{(i)})\}_{i=1}^N$$ over 2D pose $$s_t=[x\;y\;\theta]^\top,$$ seeded in RViz via “2D Pose Estimate”. 

Motion sampling uses Odometry Motion Model with standard noise parameters $$\alpha_{1\ldots4}$$:
$$
\hat{s}_t^{(i)}\sim p(s_t\mid u_t,s_{t-1}^{(i)})
$$
with angle wrapping to 
$$
[-\pi,\pi].
$$
The **beam sensor model** evaluates LIDAR likelihoods per ray using a mixture:
$$
p(z\,|\,\hat z)=w_{\mathrm{hit}}\;\mathcal N(\hat z;\,z,\sigma^2)
+w_{\mathrm{short}}\;\lambda e^{-\lambda z}\,\mathbb{I}[0\le z\le \hat z]
+w_{\max}\;\mathbb{I}[z=z_{\max}]
+w_{\mathrm{rand}}\;\tfrac{1}{z_{\max}}\mathbb{I}[0\le z< z_{\max}],
$$
with $$w_{\mathrm{hit}}+w_{\mathrm{short}}+w_{\max}+w_{\mathrm{rand}}=1.$$ Expected ranges $$\hat z$$ come from **ray-tracing** the occupancy map in the sensor frame.

Particles rapidly collapse onto the correct corridor/mirror hypothesis, avoid the mirror ambiguity inherent in the building’s symmetric layout, the posterior mean aligns with the robot footprint with low latency.

<video controls preload="metadata" width="100%" playsinline poster="/assets/img/projects/jackal/2-poster.jpg">
  <source src="/assets/video/projects/jackal/2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

---