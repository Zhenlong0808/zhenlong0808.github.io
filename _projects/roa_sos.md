---
layout: page
title: Region of Attraction Estimation for Polynomial Systems
permalink: /projects/roa-sos/
description: ROA inner-approximation via SOS with bilinear alternation (S-procedure + Lyapunov).
img: /assets/img/projects/roa/cover.webp      # 一级页面卡片封面（动图）
category: projects                              # 确保能在 /projects/ 显示（见下面说明）
importance: 1                                   # 越小越靠前
mathjax: true                                   # 本页启用 LaTeX 公式渲染
---

### Overview
For a nonlinear polynomial dynamical system
$$
\dot{\mathbf{x}} = \mathbf{f}(\mathbf{x}),\quad \mathbf{f}:\mathbb{R}^n\!\to\!\mathbb{R}^n,
$$
this project seeks a local Lyapunov function $$V(\mathbf{x})\succ 0$$ and a scalar $$\rho>0$$ whose sublevel set
$$
B(\rho)=\{\mathbf{x}\mid V(\mathbf{x})\le\rho\}
$$
is an inner estimate of the Region of Attraction (ROA) to the origin. The goal is to maximize $$\rho$$ while guaranteeing the implication
$$
V(\mathbf{x})\le\rho\;\Longrightarrow\;\dot{V}(\mathbf{x})<0.
$$

---

## S-Procedure and Sum-of-Squares Relaxation

Define the candidate Lyapunov sublevel set (whose “volume” we aim to maximize by searching the largest feasible $$\rho>0$$):
$$
B := \Bigl\{\mathbf{x}\,\Bigl|\,V(\mathbf{x}) \le \rho\Bigr\}, 
\qquad 
\dot V(\mathbf{x}) = \frac{\partial V}{\partial \mathbf{x}}\,\mathbf{f}(\mathbf{x}).
$$
Our objective is to certify the implication: whenever $$V(\mathbf{x}) \le \rho$$, we have $$\dot V(\mathbf{x}) \le 0$$,
$$
V(\mathbf{x}) \le \rho 
\;\Longrightarrow\; 
\dot V(\mathbf{x}) \le 0.
$$

**S-procedure.**  
Introduce a polynomial multiplier $$\lambda(\mathbf{x}) \ge 0$$. If
$$
\dot V(\mathbf{x}) \;\le\; \lambda(\mathbf{x})\bigl(V(\mathbf{x})-\rho\bigr),
$$
then the desired implication holds for all $$\mathbf{x}$$.

**SOS relaxation.**  
Cast the conditions as sum-of-squares (SOS) constraints:
$$
\begin{aligned}
-\dot V(\mathbf{x}) + \lambda(\mathbf{x})\bigl(V(\mathbf{x}) - \rho\bigr) &\text{ is SOS},
\lambda(\mathbf{x}) &\text{ is SOS},
V(\mathbf{x}) - \epsilon\,\mathbf{x}^\top\mathbf{x} &\text{ is SOS},\qquad \epsilon>0,
\end{aligned}
$$
Each SOS constraint ensures global non-negativity; the last enforces $$V(\mathbf{x}) \succ 0$$.

---

## Bi-level Optimization Scheme (Bilinear Alternation)

**Master problem**
$$
\begin{aligned}
\max_{\rho,\,V,\,\lambda}\quad & \rho 
\text{s.t.}\quad 
& -\dot V(\mathbf{x})+\lambda(\mathbf{x})\bigl(V(\mathbf{x})-\rho\bigr)\text{ is SOS},\\
& \lambda(\mathbf{x})\text{ is SOS},\\
& V(\mathbf{x})-\epsilon\,\mathbf{x}^{\!\top}\mathbf{x}\text{ is SOS},\;\;\epsilon>0. 
\end{aligned}
\tag{1}
$$
Problem (1) is non-convex; the following two sub-problems decouple the bilinear term and are solved in alternation.

**Step A — Multiplier update** (fixed $$V(x),\rho$$)
$$
\begin{aligned}
\max_{\lambda(\mathbf{x}),\,s}\quad & s 
\text{s.t.}\quad 
& s>0,\\
& -\dot V(\mathbf{x})+\lambda(\mathbf{x})\bigl(V(\mathbf{x})-\rho\bigr)-s \;\text{ is SOS},\\
& \lambda(\mathbf{x})\text{ is SOS}.
\end{aligned}
$$

**Step B — Lyapunov update** (fixed $$s$$)
$$
\begin{aligned}
\max_{V(\mathbf{x}),\,\rho}\quad & \rho 
\text{s.t.}\quad 
& \rho>0,\\
& -\dot V(\mathbf{x})+\lambda(\mathbf{x})\bigl(V(\mathbf{x})-\rho\bigr)\text{ is SOS},\\
& V(\mathbf{x})-\epsilon\,\mathbf{x}^{\!\top}\mathbf{x}\text{ is SOS}.
\end{aligned}
$$

**Initialization.**  
Choose a feasible quadratic seed $$V_0(\mathbf{x})=\mathbf{x}^{\!\top}P\mathbf{x}$$ from a quadratic Lyapunov equation or LQR, then alternate Step A/B until $$\rho$$ converges.

---

## Result (Van der Pol)

The nonlinear system:
$$
\dot x_1 = -x_2,\qquad
\dot x_2 = x_1-(1-x_1^{2})x_2
$$
has a limit cycle, making inner ROA enlargement non-trivial. We implement the SOS programs in **MATLAB** using **SPOT** (Systems Polynomial Optimization Tools) and solve the resulting SDPs with **SeDuMi**.

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/projects/roa/1.svg" class="img-fluid rounded z-depth-1" %}
    <div class="caption mt-2">
      Normalized phase portrait of the Van der Pol oscillator. The ellipse in the center is the certified invariant set $$B_{\text{SOS}}$$, an inner ROA estimate satisfying $$\dot{V}(\mathbf{x})<0$$.
    </div>
  </div>
</div>

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/projects/roa/3.svg" class="img-fluid rounded z-depth-1" %}
    <div class="caption mt-2">
      Evolution of the certified ellipse during SOS alternation—iterative expansion across alternation steps.
    </div>
  </div>
</div>

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/projects/roa/2.svg" class="img-fluid rounded z-depth-1" %}
    <div class="caption mt-2">
      Monte Carlo for $$B_{\text{SOS}}$$. Grey trajectories start inside and converge; red start outside and diverge beyond the limit cycle.
    </div>
  </div>
</div>

The certified region stabilizes after $$\sim 23$$ alternations and remains strictly inside the true ROA.

---

## Demo (video)
<video controls preload="metadata" width="100%" playsinline poster="/assets/img/projects/roa/poster.jpg">
  <source src="/assets/video/projects/roa/video.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
