---
layout: archive
title: Undergraduate Thesis
permalink: /undergraduate-thesis/
author_profile: true
collections: undergraduate-thesis
---

# Lid-Driven Cavity Solver (C++ with OpenMP)

This project implements a **finite difference method (FDM)** solver for the **2D incompressible Navier–Stokes equations** in a **lid-driven cavity flow** configuration. The implementation follows the **streamfunction–vorticity formulation** of the governing equations, mentioned in [IIST, 2010](https://old.iist.ac.in/sites/default/files/people/psi-omega.pdf). The above mentioned PDF also talks about discretization of the equations as well as the schemes used.

The code can be found [**here on GitHub**](https://github.com/AdityaJaiswal17/Finite_Difference_Methods/tree/main/Lid_Driven_Cavity).  

The solver is written in **C++** and provides both:  
- a **serial implementation**, and  
- a **parallel implementation** using **OpenMP**  

This allows performance comparison between the two approaches, while ensuring the correctness of the solution.  

---
