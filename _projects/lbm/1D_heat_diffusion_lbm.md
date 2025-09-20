---
title: "1D Heat Equation LBM"
layout: default
method: lbm
permalink: /projects/lbm/1D-heat-diffusion-lbm/
excerpt: "A simple implementation of the 1D heat equation using Lattice Boltzmann Method (LBM)"
---

<div style="padding-top: 7px;"></div>
# 1D Heat Conduction using Lattice Boltzmann Method (LBM)

This project implements the **Lattice Boltzmann Method (LBM)** with a **D1Q3 lattice model** and the **BGK collision operator** to simulate **1D transient heat conduction**.  
The solver is written in **C++ with OpenMP** for parallelization and outputs the temperature distribution over time until steady-state is reached.  

You can find the full code here: [GitHub Repository](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/blob/main/1D_HeadConduction_LBM/1D_heat_diff.cpp)

<figure>
  <img src="/images/self_upload/lbm/1d_heat_lbm.gif" alt="1D Heat diffusion animation created using values obtained from our code.">
  <figcaption>1D Heat diffusion animation created using values obtained from our code.</figcaption>
</figure>

---

## Methodology

### Lattice Boltzmann Method for Heat Transfer
The LBM is a **mesoscopic numerical method** that models transport phenomena by tracking the evolution of discrete distribution functions along lattice directions. For heat conduction, the governing distribution function is related to **temperature** rather than fluid density.  

- **D1Q3 Lattice Model:**  
  Each node has 3 discrete velocity directions:  
  \\[
  C = \{0, +1, -1\}
  \\]  
  with corresponding weights:  
  $$
  W = \left\{ \tfrac{2}{3}, \tfrac{1}{6}, \tfrac{1}{6} \right\}
  $$


- **BGK Collision Operator:**  
  Relaxation toward equilibrium distribution is handled by the single relaxation time (SRT) approximation:  
  \\[
  f_i(x,t+1) = f_i(x,t) - \frac{1}{\tau} \left(f_i(x,t) - f_i^{eq}(x,t)\right)
  \\]  

- **Equilibrium Distribution:**  
  For thermal problems, the equilibrium function is defined as:  
  \\[
  f_i^{eq}(x,t) = W_i \, T(x,t)
  \\]  
  where \\( T(x,t) \\) is the local temperature.

- **Thermal Diffusivity Relation:**  
  The thermal diffusivity \\(\alpha\\) is related to the relaxation parameter as:  
  \\[
  \alpha = \frac{1}{3} \left(\tau - \tfrac{1}{2}\right)
  \\]  

Since LBM inherently models **time evolution**, the solver is **transient**, meaning the simulation progresses over iterations until a steady-state temperature distribution is reached.

---

## Boundary Conditions
- **Dirichlet conditions:**  
  - Left boundary: \\(T = 100\\)  
  - Right boundary: \\(T = 100\\)  
- **Heat source:**  
  A fixed high temperature of \\(T = 1000\\) is applied at the center of the domain.  
- **Interior nodes:**  
  Updated by streaming and collision steps.  

---

## Output Structure
The solver generates results in two forms:

1. **Time-resolved outputs**  
   - Stored in the folder `output_folders/`  
   - Files: `temp_output_t<time>`  
   - Each file contains temperature distribution at iteration `t`.  

   Format: 

    1. **x** - Nodes along x axis.
    2. **T(x,t)** - Temperature profile, varying with x and time.

2. **Final output**  
- File: `output.dat`  
- Contains the steady-state temperature distribution. 


---

##  Validation
The computed temperature profiles are compared with the **analytical solution of the 1D steady-state heat conduction equation**:  

\\[
\frac{\partial^2 T}{\partial x^2} = 0
\\]  

With boundary conditions applied at both ends and a heat source in the center.  
The results match the analytical solution well, validating the correctness of the LBM formulation.  

<div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/lbm/heat_1d_lbm.png" alt="Computed temperature profile at steady state, gives a straight line which validates our code." style="width: 90%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Computed temperature profile at steady state, gives a straight line which validates our code.
    </figcaption>
  </figure>
</div>
---

##  Key Features
- Implements **LBM (D1Q3, BGK)** for thermal problems.  
- Handles **transient evolution** until steady-state is achieved.  
- Flexible output at regular intervals for visualization.  

---
