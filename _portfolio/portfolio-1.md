---
title: "CurvedVoronoi"
excerpt: "Compute Voronoi diagrams in spherical or hyperbolic spaces <br/><img src='https://github.com/adfwer233/CurvedVoronoi/raw/main/assets/spherical/1024_voronoi.png' style='width: 200px; height: 200px;'><img src='https://github.com/adfwer233/CurvedVoronoi/raw/main/assets/hyperbolic/256_voronoi.png' style='width: 200px; height: 200px;'>"
collection: portfolio
---

This project was finished in **[Computational Geometry Course](https://dsa.cs.tsinghua.edu.cn/~deng/cg/index.htm)** at Tsinghua University.

# Computing Voronoi Diagrams in Constant Curvature Spaces

## Background and Problem Description

In this project, we explore methods for computing Voronoi diagrams in constant curvature spaces. As shown in the table below, there are three types of constant curvature spaces: Euclidean space, spherical space, and hyperbolic space, each equipped with different metrics that induce distinct geometries.

| | Euclidean Space | Spherical Space | Hyperbolic Space |
|---|---|---|---|
| Curvature | Zero | Positive | Negative |
| Parallel Lines | 1 | 0 | $\infty$ |
| Triangle Angle Sum | $\pi$ | > $\pi$ | < $\pi$ |

*Table: Classification of constant curvature spaces*

In this project, we aim to compute Voronoi diagrams in two-dimensional constant curvature spaces. Formally, given a point set $\\lbrace P_i\\rbrace_{1 \leq i \leq n}$, we want to partition the constant curvature space $(S, d)$ into regions $\lbrace R_i\\rbrace_{1 \leq i \leq n}$ such that:

$$
R_i = \lbrace x \mid d(x, P_i) < d(x, P_j), \forall  i \neq j\rbrace
$$

## Space of Spheres

For computing Voronoi diagrams in Euclidean space, there are many classical methods. One particularly insightful approach involves lifting points to a paraboloid and reducing the problem to computing a lower convex hull. Specifically, for any point $P = (x, y)$, we lift it to a point $Q = (x, y, x^2 + y^2)$ on the paraboloid $z = x^2 + y^2$. Computing the lower convex hull of the point set $\lbrace Q_i\rbrace$ and projecting it back to the plane gives us the Delaunay triangulation of the original point set. Taking the dual of this triangulation yields the Voronoi diagram. The figure below illustrates this process.

![Euclidean Delaunay triangulation can be reduced to lower convex hull computation](assets/planar.png)

Let's understand why this algorithm works. Define $\mathcal{C}$ as the set of all circles in the 2D plane. There exists a bijection from $\mathcal{C}$ to $\mathbb{R}^3$ that maps a circle with center $(x, y)$ and radius $r$ to the point $(x, y, x^2 + y^2 - r^2)$ in $\mathbb{R}^3$. We call this $\mathbb{R}^3$ with this interpretation the "Space of Spheres." Let $\Gamma$ be the paraboloid $z = x^2 + y^2$. We can prove several properties of the Space of Spheres:

1. Each point on $\Gamma$ represents a circle of radius zero, which corresponds to its projection in $\mathbb{R}^2$.
2. For each point $p$ on $\Gamma$, the tangent plane $\phi(q)$ at point $p$ corresponds to the set of circles passing through point $p$.
3. For any two distinct points $p,q$, $\phi(p) \cap \phi(q)$ is a line whose projection represents the perpendicular bisector of the two points.
4. For any three distinct points $p, q, r$, $\phi(p) \cap \phi(q) \cap \phi(r)$ corresponds to their circumcircle.
5. For each point in the Space of Spheres, its polar plane with respect to $\Gamma$ intersects $\Gamma$ at points corresponding to the circle represented by that point.
6. For every plane that intersects $\Gamma$, its corresponding pole represents a circle. Points inside this circle (on $\Gamma$) lie below the plane, while points outside lie above the plane.

Consider the triangulation given by projecting the lower convex hull. For each triangle in this triangulation, its preimage in the Space of Spheres is a face in the convex hull. Due to the lower convex hull property, there are no points from $\lbrace Q_i\rbrace$ below this face. Since the pole corresponding to this face represents the triangle's circumcircle, we know this circumcircle is empty. Therefore, the resulting triangulation satisfies the empty circle property and is thus a Delaunay triangulation.

## Voronoi Diagrams on the Sphere

![Space of spheres](assets/space_of_spheres.png)

In this section, we reduce the computation of Voronoi diagrams on the sphere to 3D convex hull computation, following the approach by [Caroli et al., 2010]. For ease of explanation, we assume the origin is inside the convex hull of the given point set. For the sphere $S$, we find that each geodesic circle on the sphere corresponds to a point in 3D space. For every geodesic circle on the sphere (with radius less than $\pi r / 2$, intuitively covering less than half the sphere), we can prove that it is also a circle in the 3D embedding. We can find the pole of the plane containing this circle with respect to the sphere. Conversely, for each point in 3D space, the intersection of its polar plane with the sphere corresponds to a geodesic circle on the sphere. Thus, we establish a one-to-one correspondence between arbitrary points in 3D space and geodesic circles on the sphere. This correspondence also has several properties:

1. Each point on $S$ represents a circle of radius zero.
2. For each point $p$ on $S$, the tangent plane at point $p$ corresponds to the set of circles passing through point $p$.
3. For any two distinct points $p,q$, $\phi(p) \cap \phi(q)$ is a line whose projection represents the perpendicular bisector of the two points.
4. For any three distinct points $p, q, r$, $\phi(p) \cap \phi(q) \cap \phi(r)$ is a point corresponding to the circumcircle of $p, q, r$.
5. For each point in the Space of Spheres, its polar plane with respect to $S$ intersects $S$ at points corresponding to the circle represented by that point.
6. For every plane that intersects $S$, its corresponding pole represents a circle. Points inside this circle (on $S$) lie above the plane (on the opposite side from the origin), while points outside lie below the plane.

For a point set $\lbrace P_i\rbrace$ on the sphere, we compute its 3D convex hull. Projecting the 3D convex hull onto the sphere gives the Delaunay triangulation, and taking the dual yields the corresponding Voronoi diagram.

## Voronoi Diagrams on the Poincaré Disk

We use the Poincaré disk as our model of hyperbolic geometry. For other hyperbolic geometry models, we can convert between them using isometric isomorphisms. Unlike the planar and spherical cases, Delaunay triangulation doesn't always exist on the Poincaré disk, but Voronoi diagrams always exist. We define the dual of a point set's Voronoi diagram as the Delaunay complex of that point set. For a given point set, we compute its Delaunay triangulation in Euclidean space and call this result the Euclidean Delaunay complex.

There is a close relationship between the Euclidean Delaunay complex and the hyperbolic Delaunay complex. Consider the circumcircle of a triangle in the Delaunay triangulation under the Euclidean metric. Under the hyperbolic metric, this circumcircle remains a circle, though with a different center. Therefore, an empty circle in the Euclidean sense must also be an empty circle in the hyperbolic sense.

[Devillers et al., 1992] proved the following result: The hyperbolic Delaunay complex is always a subcomplex of the Euclidean Delaunay complex. For any simplex in the Euclidean Delaunay complex, it exists in the hyperbolic Delaunay complex if and only if there exists an empty circle containing this simplex.

Based on this result, we can first compute the Delaunay complex in the Euclidean metric, then filter the simplices to find those satisfying the above condition and retain them in the hyperbolic Delaunay complex. [Bogdanov et al., 2013] provided a fast method for determining this condition. For each simplex, we can find the set of all possible circumcircles in the Space of Spheres. Specifically, for a 2-simplex, this set is a point. For a 1-simplex, this set is a line segment, ray, or line. Formally, for an $n$-simplex $\sigma$ with vertices $p_1, \cdots p_n$ and adjacent vertices $q_1,\cdots q_m$, the set of possible circumcircles is:

$$
u(\sigma) = \left(\cap_{i = 1}^n \phi(p_i)\right) \cap \left( \cap_{j = 1}^m \Phi(q_j)\right)
$$

where $\Phi(q)$ represents the upper half-space of $\phi(q)$. Define $\mathcal{H_{\infty}} = (0, 0, -1)$ and $\pi_{\infty}$ as the polar plane of $\mathcal{H_\infty}$. 
Then there exists an empty circle in the disk containing $\sigma$ if and only if the projection of $u(\sigma)$ from point $\mathcal{H_\infty}$ to plane $\pi_{\infty}$ intersects 

$$B_{\pi_\infty} = \lbrace (x, y, 1) \mid x^2 + y^2 < 1\rbrace.$$

Thus, we can compute the hyperbolic Delaunay complex and take its dual to obtain the Voronoi diagram in the hyperbolic sense.

## Implementation Details

### Geometric Primitives on the Sphere

#### Spherical Circumcenter

For three points $p, q, r$ on the sphere, their circumcenter in spherical geometry is given by:

$$
c = \frac{p + q + r}{\Vert p + q + r \Vert}
$$

### Geometric Primitives on the Hyperbolic Disk

#### Hyperbolic Geodesics

The hyperbolic geodesic through two points $p, q$ is a circular arc. To visualize it, we compute its discretization as follows:

1. Use a Möbius transformation $f$ to map $p$ to the center
2. Uniformly discretize the line segment from $f(p)$ to $f(q)$
3. Use $f^{-1}$ to transform the discretization back to the original space

#### Hyperbolic Circumcenter

For three points $p, q, r$, we can compute their Euclidean circumcenter $C_E$. Their hyperbolic circumcenter $C_H$ lies on the same line as the origin and $C_E$, and satisfies:

$$
\Vert C_h \Vert = \frac{1 + \Vert C_H \Vert^2}{1 + \Vert C_E\Vert ^2 - r^2} \Vert C_E\Vert
$$

Solving this quadratic equation and taking the smaller root.

#### Hyperbolic Bisector

For two points $p, q$, their hyperbolic bisector is also a circular arc, represented in the space of spheres as:

$$
s = \alpha p + (1 - \alpha)q
$$

where

$$
\alpha = \frac{1 - \Vert p \Vert^2}{\Vert p \Vert^2 - \Vert q \Vert^2}
$$

Here, the norm is computed in the Euclidean disk.

## Results

We implemented the above algorithms in C++. Rendering and interaction capabilities are supported by another project of the author's, which provides some wrappers for Vulkan and ImGui, helping us quickly implement 2D and 3D mesh rendering with simple interaction.

The figure below shows the computation process and results of Voronoi diagrams on the sphere. First, we compute the 3D convex hull, project the hull onto the sphere to get the Delaunay triangulation, and then take the dual to obtain the Voronoi diagram on the sphere.

**16 points:**

| Convex Hull | Delaunay Triangulation | Voronoi Diagram |
|-------------|----------------------|----------------|
| ![16 Convex Hull](assets/16_convex.png) | ![16 Delaunay](assets/16_delaunay.png) | ![16 Voronoi](assets/16_voronoi.png) |

**64 points:**

| Convex Hull | Delaunay Triangulation | Voronoi Diagram |
|-------------|----------------------|----------------|
| ![64 Convex Hull](assets/64_convex.png) | ![64 Delaunay](assets/64_delaunay.png) | ![64 Voronoi](assets/64_voronoi.png) |

**1024 points:**

| Convex Hull | Delaunay Triangulation | Voronoi Diagram |
|-------------|----------------------|----------------|
| ![1024 Convex Hull](assets/1024_convex.png) | ![1024 Delaunay](assets/1024_delaunay.png) | ![1024 Voronoi](assets/1024_voronoi.png) |

The figure below shows the computation process and final results of Voronoi diagrams on the Poincaré disk. We first compute the standard Delaunay triangulation, then filter the resulting simplices, and finally take the dual to obtain the Voronoi diagram on the Poincaré disk.

**64 points:**

| Euclidean Delaunay | Hyperbolic Delaunay | Voronoi Diagram |
|-------------------|-------------------|----------------|
| ![64 Euclidean Delaunay](assets/hyperbolic/64_e_complex.png) | ![64 Hyperbolic Delaunay](assets/hyperbolic/64_h_complex.png) | ![64 Voronoi Diagram](assets/hyperbolic/64_voronoi.png) |

**256 points:**

| Euclidean Delaunay | Hyperbolic Delaunay | Voronoi Diagram |
|-------------------|-------------------|----------------|
| ![256 Euclidean Delaunay](assets/hyperbolic/256_e_complex.png) | ![256 Hyperbolic Delaunay](assets/hyperbolic/256_h_complex.png) | ![256 Voronoi Diagram](assets/hyperbolic/256_voronoi.png) |

**2048 points:**

| Euclidean Delaunay | Hyperbolic Delaunay | Voronoi Diagram |
|-------------------|-------------------|----------------|
| ![2048 Euclidean Delaunay](assets/hyperbolic/2048_e_complex.png) | ![2048 Hyperbolic Delaunay](assets/hyperbolic/2048_h_complex.png) | ![2048 Voronoi Diagram](assets/hyperbolic/2048_voronoi.png) |

## Summary

We implemented Voronoi diagram computation in constant curvature spaces beyond Euclidean space. The algorithm can be summarized as follows:

- For the spherical case, the problem can be reduced to 3D convex hull computation. After computing the convex hull, project the faces onto the sphere to get the Delaunay triangulation, then take the dual to obtain the Voronoi diagram.
- For the Poincaré model of hyperbolic geometry, first compute the Delaunay triangulation in Euclidean space, then filter the simplices to get the Delaunay complex, and finally take the dual to obtain the Voronoi diagram.

## References

1. Caroli, M., Teillaud, M., & Vegter, G. (2010). *Robust and efficient Delaunay triangulations of points on or close to a sphere.*
2. Devillers, O., & Teillaud, M. (1992). *The space of spheres, a geometric tool to unify duality results on Voronoi diagrams.*
3. Bogdanov, M., Devillers, O., & Teillaud, M. (2013). *Hyperbolic Delaunay complexes and Voronoi diagrams made practical.*

Code
===

Implemented by C++ and CMake.

- [Github](https://github.com/adfwer233/CurvedVoronoi)