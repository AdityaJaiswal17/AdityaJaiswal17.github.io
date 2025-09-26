---
title: "Lid Driven Cavity LBM"
layout: default
method: lbm
permalink: /projects/lbm/lid-driven-cavity-lbm/
excerpt: "Lid-driven cavity simulation using Lattice Boltzmann Method (D2Q9 model)."
---
<div style="padding-top: 7px;"></div>

# Lid-Driven Cavity Solver (C++ LBM)

This project implements a **Lattice Boltzmann Method (LBM)** solver for the **2D incompressible flow** in a **lid-driven cavity** configuration. The implementation uses the **D2Q9 velocity model** and the **BGK collision operator**.  

The code can be found [**here on GitHub**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/blob/main/Lid_Driven_Cavity/cavity.cpp).  

The solver is written in **C++** and outputs **full-field velocity and density data** for further post-processing and visualization.  

---

## Animations

Two animations are generated from the simulation outputs:

<div style="display: flex; justify-content: space-around; gap: 20px; align-items: flex-start;">

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/lbm/Re_100.gif" 
         alt="LBM Animation for Re = 100" 
         style="width: 100%; max-height: 500px; object-fit: contain;">
    <figcaption>Velocity field animation for \(Re = 100\).</figcaption>
  </figure>

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/lbm/Re_500.gif" 
         alt="LBM Animation for Re = 500" 
         style="width: 100%; max-height: 500px; object-fit: contain;">
    <figcaption>Velocity field animation for \(Re = 500\).</figcaption>
  </figure>

</div>

## Methodology

### Governing Equations
The simulation uses the **Lattice Boltzmann Equation (LBE)** with the **BGK approximation**:

$$
f_i(\mathbf{x} + \mathbf{c}_i \Delta t, t+\Delta t) = f_i(\mathbf{x},t) - \frac{1}{\tau} \Big( f_i(\mathbf{x},t) - f_i^{eq}(\mathbf{x},t) \Big)
$$

where:  
- \\(f_i\\) = particle distribution function in direction \\(i\\)  
- \\(\mathbf{c}_i\\) = discrete velocity vector  
- \\(\tau = 3\nu + 0.5\\) = relaxation parameter related to kinematic viscosity \\(\nu\\)  
- \\(f_i^{eq}\\) = equilibrium distribution function:  

$$
f_i^{eq} = w_i \, \rho \Big[ 1 + 3 (\mathbf{c}_i \cdot \mathbf{u}) + \frac{9}{2} (\mathbf{c}_i \cdot \mathbf{u})^2 - \frac{3}{2} \mathbf{u}^2 \Big]
$$

The macroscopic properties are obtained as:

$$
\rho = \sum_{i=0}^{8} f_i, \quad
\mathbf{u} = \frac{\sum_{i=0}^{8} f_i \mathbf{c}_i}{\rho}
$$

---

### Lattice Model (D2Q9)
- **Discrete velocity set:** 9 directions (D2Q9)  
- **Weights:**  
  - \\(w_0 = \tfrac{4}{9}\\)  
  - \\(w_{1-4} = \tfrac{1}{9}\\)  
  - \\(w_{5-8} = \tfrac{1}{36}\\)  
---

### Computational Setup
- Grid resolution: \\(101 \times 101\\)  
- Maximum iterations: \\(10^5\\)  
- Simulation stops early if velocity residuals satisfy convergence tolerance:  
  - \\(res_{u_x} \le 10^{-7}\\)  
  - \\(res_{u_y} \le 10^{-5}\\)  
- Time step and lattice spacing are unity in lattice units.  

---

### Boundary Conditions
- **Top wall (lid):** moving with \\(U_\text{wall} = 0.1\\) (lattice units)  
  - Implemented using a **modified bounce-back** scheme.  
- **Bottom, left, right walls:** no-slip boundaries (standard bounce-back).  

---

### Collision and Streaming
1. **Collision step (BGK model):**  

$$
f_i^* = f_i (1-\tau^{-1}) + f_i^{eq} \, \tau^{-1}
$$  

2. **Streaming step:**  

$$
f_i(\mathbf{x} + \mathbf{c}_i, t+1) = f_i^*(\mathbf{x},t)
$$  

---

### Output Structure
Each saved timestep (`output_t*.dat`) contains:  
1. x-index  
2. y-index  
3. \\(u_x\\)  
4. \\(u_y\\)  
5. velocity magnitude $$(|\mathbf{u}|)$$  
6. density \\(\rho\\)  

---

## Results

### Flow Characteristics
- At \\(Re = 100\\), the primary vortex forms at the cavity center.  
- Increasing the Reynolds number (e.g., \\(Re = 500\\)) shows stronger corner vortices and more complex flow structures.  

---

## Validation
- Simulation results were validated against **Ghia et al. (1982)** benchmark data.  
- Non-dimensionalization is used for comparison:  

$$
U^* = \frac{u_x}{U_\text{wall}}, \quad
V^* = \frac{u_y}{U_\text{wall}}, \quad
x^* = \frac{x}{L}, \quad
y^* = \frac{y}{L}
$$  

**Validation Figures:**  

<figure style="display: flex; justify-content: center; gap: 20px; align-items: center;">
  <img src="/images/self_upload/lbm/validation_UY.png" alt="Computational vs experimental value for U_x=0.5" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <img src="/images/self_upload/lbm/validation_XV.png" alt="Computational vs experimental value for V_y=0.5" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <figcaption style="text-align:center; margin-top:8px;">
    Computed values of velocity obtained at midlines of the cavity, \(U_x \, @ \, x = 0.5\) (left) and \(V_y \, @ \, y = 0.5\) (right) compared with the literature values from <i>Ghia et al.</i> for the configuration when <b>Reynolds number = 100</b>.
  </figcaption>
</figure>


