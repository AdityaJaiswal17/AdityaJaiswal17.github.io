---
title: "Couette flow LBM"
layout: project
method: lbm
permalink: /projects/fdm/couette-flow-lbm/
excerpt: "Couette flow implementation using Lattice boltzmann method with BGK."
---
Couette flow with U_wall set to 0.1. Right & Left walls habe periodic boundary conditions applied to them. To run, just compile and execute.

Test file is created to compare the analytical and computational results. Reyolds number = 100 in the code and the viscosity is set to 0.1 which also determines the relaxation factor (tau) value owing to the BGK Model nature.

Validation file (png) is also uploaded and non dimensionalization of variables in necessary for the comparison amongst the computation & analytical value.

The codes to this example can be accessed by this [**link**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/blob/main/CouetteFlow/CouetteFlow.cpp)

--insert images--