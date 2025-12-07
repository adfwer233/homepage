---
title: 'Walkin Robin Notes'
date: 2025-10-11
permalink: /posts/2025/10/robin/
tags:
  - Monte Carlo Solvers
---

My Notes for Walkin' Robin paper.


Problem
===

We consider the Poisson equation with Robin boundary conditions as follows:

$$
\Delta u(x) = f(x) \quad x \in \Omega
$$

$$
u(x) = g(x) \quad x \in \partial \Omega_D
$$

$$
\frac{\partial u}{\partial n}(x)  + \mu (x) u(x) = h(x) \quad x \in \partial \Omega_R
$$

$\mu(x) \geq 0$ is a non-negative Robin coefficient.

**Limitation Behaviors**: As $\mu \to 0$, Robin conditions go back to the Neumann condition. On the other hand, as $\mu \to \infty$, Robin conditions go back to Dirichlet boundary condition. Due to following formulation:

$$
\frac{1}{\mu(x)} \frac{\partial u}{\partial n}(x)  + u(x) = \frac{h(x)}{\mu(x)} \quad x \in \partial \Omega_R
$$


Stochastic Perspective
===

From the perspective of stochastic process, the Robin condition is equivalent to the *partially reflected Brownian motion*. Here we provide a brief review of the mathematical foundations.

Some notations:
- Brownian motion: $W_t$
- Harmonic measure: $\omega_x$

> **Definition of Harmonic Measure**
> 
> $\Omega$ is a domain in $\mathbb{R}^n$. For any point $x \in \Omega$, we define the random variable $T_x = \inf\{t > 0 \mid x + W_t \in \partial \Omega\}$, which is called *hitting time*.
> 
> For any Borel set $A \subset \partial \Omega$, its **harmonic measure** is defined as 
> 
> $$\omega_x(A) = \mathbb{P}\lbrace W_{T_x} \in A , T_x < \infty \rbrace$$

A well known result is that the expectation of $u(W_{T_x})$ under the harmonic measure is the solution of Laplace equation with the Dirichlet boundary condition, which is the foundation of Walk on Sphere algorithm. 

For Neumann boundary conditions, this formulation failed. The normal derivative representing a flux leads to the reflection on the boundary.

We use stochasitc differential equation to define the so-called *reflected Brownian motion*.

> **SDE of Reflected Brownian Motion**
> 
> Let $\Omega$ be a domain and $n(s)$ is a vector-valued function on $\partial \Omega$. For a given point $x \in \Omega$, consider the stochastic equation in the following form:
> $$d \hat{W}_t = dW_t + n(\hat{W}_t) I_{\partial \Omega}(\hat{W}_t) d \mathcal{l}_t$$
> where $W_t$ is the Brownian motion and $I_{\partial \Omega}$ is the indicator of the boundary $\partial \Omega$. By a solution of this equation, we mean a pair of almost surely continuous process $W_t$ and $l_t$, satisfying the above equation, adapted to the underlying family of $\sigma$-fields and satisfying following equations with probability one:
> - $\hat{W}_t$ belongs to $\Omega \cup \partial \Omega$
> - $l_t$ is a non-decreasing process, which increases only for $t \in \mathcal{T}$, $\mathcal{T} = \lbrace t > 0 \mid \hat{W}_t \in \partial \Omega \rbrace$ having Lebesgue measure zero almost surely.

Following theorem guarantees the existence and uniqueness in the case of smooth boundaries, which gives the definition of reflected Brownian motion.

> **Theorem (Existence and uniqueness of reflected Brownian motion)**
> 
> Let $\Omega$ be a domain with $C^2$ boundary. $n(s)$ is the vector of the inward unit normal at boundary point $s$. For a given point $x \in \Omega \cup \partial \Omega$, the stochastic equation possesses a unique solution, i.e., there exist the reflected Brownian motion $\hat{W}_t$ and the local time on the boundary $l_t$ satisfying the above conditions. The solution is also unique. 

Now we define the **partially** reflected Brownian motion (PRBM). It is not a completely new process. PRBM reproduces the reflected Brownian motion up to a moment and terminated here.

> **Definition(Partially Reflected Brownian Motion)**
>
> For a given domain $\Omega$ with smooth boundary. Let $\hat{W}_t$ be the reflected Brownian motion started from $x \in \Omega \cup \partial \Omega$, and let $l_t$ be the related local time process. Let $\chi$ be a random variable, independent of $\hat{W}_t$ and $l_t$, distributed according to the exponential law with a positive parameter $\Lambda$:
> $$ \mathbb{P} \lbrace \chi \geq \lambda \rbrace = \exp(- \lambda / \Lambda) $$
> The stopping time
>
> $$T^x_\Lambda = \inf \lbrace t > 0 \mid l_t \geq \chi \rbrace$$
>
> gives the first moment when the local time process $l_t$ exceeds the random variable $\chi$. The process $\hat{W}$ conditioned to stop at random moment $t = T^x_{\lambda}$ is called partially reflected Brownian motion (PRBM).

In the particular case $\Lambda = 0$, the exponential distribution is degenerated. Stopping time becomes the first hit time and the PRBM is degenerated to the full absorption.

Boundary Integral Equation Perspective
===

Recall the boundary integral equation

$$
\alpha(x) u(x) = \int_{\partial A} P^C(x, z) u(z) + G^C(x, z) \frac{\partial u(z)}{\partial n_z} \mathrm{d} z + \int_A G^C(x, y) f(y) \mathrm{d} y
$$

$A$ and $C$ are arbitrary subsets of $\Omega$ and $\mathbb{R}^N$ respectively. Walk on star algorithm choose $A = St = B \cap \Omega$ and $C = B$, where $B$ is the ball centered at $x$ with radius $R$ equals the minimum of the distances to $\partial \Omega_D$ and the closet silhouette point on $\partial \Omega_N$.

Since $G^B(x, y)$ vanishes on $\partial B$, the equations becomes

$$
\alpha(x) u(x) = \int_{\partial St(x, R)} P^B(x, z)u(z) \mathrm{d} z + \int_{\partial St_N(x, R)} G^B(x, z)h(z) \mathrm{d} z + \int_{St(x, R)} G^B(x, y) f(y) \mathrm{d} y
$$

In the Robin case, the key is so-called *Brakhage-Werner trick* from potential theory. 

Substitute $\frac{\partial u}{\partial n} = \mu \cdot u + h$ to the previous equation, we get:

$$
\alpha(x)u(x) = \int_{\partial St(x, R)} \rho_{\mu}(x, z)P^B(x, z) u(z) \mathrm{d} z + \int_{\partial St_R(x, R)} G^B(x, z) h(z) \mathrm{d} z + \int_{St(x, R)} G^B(x, y) f(y) \mathrm{d} y
$$

where $\rho_{\mu}(x, z)$ is defined by

$$
\rho_\mu(x, z) = \begin{cases}
1 - \mu(z) \frac{G^B(x, z)}{P^B(x, z)}, & \text{on}~ \partial St_R \\
1, & \text{on}~ \partial St_B
\end{cases}
$$

With this formulation, we can build a Monte Carlo estimator. But note that, to keep the recursive one-sample Monte Carlo estimation convergence, we should keep the coefficient $\rho_{\mu}(x, z) \in [0, 1)$.

Select Ball Radius
===

We substitute the Possion kernel and Green's function to the formulation of $\rho_\mu$. ($P^B(x, z)$ is eliminated during the importance sampling).

$$
\rho_\mu(x, z) = 1 - \frac{u(z)r}{\cos \theta} \left(1 - \frac{r}{R}\right)
$$

We want to restrict $\rho_\mu$ to $[0 , 1]$, hence we require

$$
\frac{\mu(z)r}{\cos \theta} \left(1 - \frac{r}{R}\right) \leq 1
$$

where $r = |x - z|$, $\cos \theta = n_z \cdot (z - x) / r$ and $R$ is the distance to closet silhouette.

We can find a upper bound of $R$. If $r \leq \frac{\mu(z)}{\cos \theta}$, the condition is always satisfied, and:

$$
R \leq \frac{r}{1 - \frac{\cos \theta}{\mu(z) r}} ~\text{when}~ r > \frac{\cos \theta}{\mu(z)}
$$

It's worth to note the relation between $r$ and $\cos \theta$. For triangle meshes, the distance between $x$ and a triangular face $t$ is a constant value $h$, and $r \cos \theta = h$. Plug in this equation to previous upper bound, we have

$$
R \leq \frac{\mu(z) h}{\mu(z) h \cos \theta - \cos^3 \theta}, ~ \cos \theta \leq \sqrt{\mu(z) h}
$$

The upperbound is minized as $\mu$ take its maximum, hence

$$
R = \frac{\mu_{\max}h}{\mu_{\max}h\cos \theta - \cos^3 \theta}
$$
is always a good choice.

By a simple analysis, we found that the upperbound is minized as $\cos \theta = \sqrt{\mu_{\max}h / 3}$. So we take $\cos \theta = Clamp(\sqrt{\mu_{\max}h / 3}, \cos_{\min}, \cos_{\max})$ in expression of $R$.
