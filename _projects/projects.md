---
layout: archive
title: Projects
permalink: /projects/
author_profile: true
collections: projects
---

{% include base_path %}

Welcome to my collection of computational methods and numerical analysis projects.

All the codes are written in C++ along with openMP implementation in some of them (has been mentioned wherever needed). All the data was visualized using [**gnuplot**](http://www.gnuplot.info/) after the results, users can modify and use various ways like [**matplotlib**](https://matplotlib.org/) as well for the visualization.

**Note: All codes presented here were independently developed by me. The links provided lead directly to my GitHub repository.**

Numerical Methods
======

* [**Finite Difference Methods**](/projects/fdm/)
======
## About the Finite Difference Method (FDM)

The **Finite Difference Method (FDM)** is a **deterministic numerical technique** that solves partial differential equations (PDEs) by directly discretizing their differential operators over a computational grid. In this approach, derivatives are approximated by difference quotients, converting the continuous governing equations into a system of algebraic equations that can be solved iteratively or directly.  

Key steps include:  
1. **Domain discretization:** The spatial and temporal domain is divided into a structured grid.  
2. **Differential approximation:** Derivatives (first- and second-order) are replaced with finite difference stencils (e.g., forward, backward, or central schemes).  
3. **Equation assembly & solution:** The resulting system is solved using iterative methods until convergence.  

* [**Lattice Boltzmann Method**](/projects/lbm/)
======
## About the Lattice Boltzmann Method (LBM)

The **Lattice Boltzmann Method (LBM)** is a **mesoscopic, transient solver** for fluid dynamics that models the evolution of particle distribution functions on a discrete lattice. Unlike traditional CFD approaches that directly solve the Navier–Stokes equations, LBM is based on the **Boltzmann transport equation** with simplified collision models (e.g., BGK approximation).  

The method works by alternating between two main steps:  
1. **Streaming:** Particle distributions move along discrete lattice directions.  
2. **Collision:** Local relaxation drives the system toward an equilibrium distribution.  

Through these steps, LBM captures the **time-dependent (transient) evolution** of the flow field, from initialization until it reaches a steady or statistically stationary state. Unlike, the finite difference method, the discretized equation is not solve directly but the important variables like velocity, pressure, temperature,... etc are recovered indirectly by solving for discretized boltzmann equation which is known as Lattice Boltzmann equation.


---

*Last updated: {{ site.time | date: "%B %Y" }}*