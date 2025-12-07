---
title: "Off-Centered WoS-Type Solvers with Statistical Weighting"
collection: publications
category: conferences
permalink: /publication/reswos
excerpt: Use statistical weighting in WoS solvers to reduce variance and correlation artifacts.
date: 2025-11-01
venue: 'SIGGRAPH Asia 2025 Conference Proceeding'
slidesurl: # 'https://baoanchang.com/files/slides1.pdf'
paperurl: 'https://arxiv.org/pdf/2510.25152'
bibtexurl: # 'https://baoanchang.com/files/bibtex1.bib'
citation: # 'Your Name, You. (2009). &quot;Paper Title Number 1.&quot; <i>Journal 1</i>. 1(1).'
---

![](/images/reswos/assets/teaser_figure.jpg)

*We propose a statistical weighting strategy for off-centered Monte Carlo estimators, demonstrating remarkable variance reduction results. The first row demonstrates error maps, and the results are shown in the second row. Compared with both vanilla walk on sphere algorithm and different variance reduction methods, our method reaches better mean square error within the same time.*

Abstract
===

Stochastic PDE solvers have emerged as a powerful alternative to traditional discretization-based methods for solving partial differential equations (PDEs), especially in geometry processing and graphics. While off-centered estimators enhance sample reuse in WoS-type Monte Carlo solvers, they introduce correlation artifacts and bias when Green's functions are approximated. In this paper, we propose a statistically weighted off-centered WoS-type estimator that leverages local similarity filtering to selectively combine samples across neighboring evaluation points. Our method balances bias and variance through a principled weighting strategy that suppresses unreliable estimators. We demonstrate our approach's effectiveness on various PDEs—including screened Poisson equations—and boundary conditions, achieving consistent improvements over existing solvers such as vanilla Walk on Spheres, mean value caching, and boundary value caching. Our method also naturally extends to gradient field estimation and mixed boundary problems.

Implementation
===

Code: [Github](https://github.com/adfwer233/StatWoS)