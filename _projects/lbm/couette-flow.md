---
title: "Couette flow LBM"
layout: default
method: lbm
permalink: /projects/fdm/couette-flow-lbm/
excerpt: "Couette flow implementation using Lattice boltzmann method with BGK."
---
<div style="padding-top: 7px;"></div>

# Couette Flow Simulation using Lattice Boltzmann Method (LBM)

This project implements the **Lattice Boltzmann Method (LBM)** for solving the **Couette flow problem** – a fundamental benchmark case in fluid dynamics. The simulation captures the velocity distribution between two parallel plates, where the top plate moves with constant velocity and the bottom plate remains stationary.

The code can be found [**here in the GitHub repository**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/blob/main/CouetteFlow/CouetteFlow.cpp).

---

## Mathematical Formulation

The Couette flow is governed by the **incompressible Navier–Stokes equations**. In the LBM framework, these are solved indirectly through the **BGK approximation** of the Boltzmann transport equation:

$$
f_i(\mathbf{x} + \mathbf{c}_i \Delta t, t + \Delta t) - f_i(\mathbf{x}, t) 
= -\frac{\Delta t}{\tau} \big( f_i(\mathbf{x}, t) - f_i^{eq}(\mathbf{x}, t) \big)
$$

where  
- \\( f_i \\) is the particle distribution function,  
- \\( f_i^{eq} \\) is the equilibrium distribution,  
- \\( \tau \\) is the relaxation time,  
- \\( \mathbf{c}_i \\) are the discrete lattice velocities.  

The velocity field is extracted using the relation:

$$
\mathbf{u} = \frac{\sum_i f_i \mathbf{c}_i}{\rho}
$$

with \(\rho\) being the fluid density.

---

## Boundary Conditions

- **Bottom wall (y = 0):** Stationary wall (no-slip condition).  
- **Top wall (y = H):** Moves with constant velocity \\( U_0 \\) in the \\(x\\)-direction.  
- **Periodic in x:** Left and right boundaries are periodic to simulate an infinite channel.

---

## Implementation Details

- **Language:** C++  
- **Parallelization:** OpenMP  
- **Collision Model:** Single-relaxation-time BGK model  
- **Lattice Structure:** D2Q9 model  

Two implementations are provided:
- **Serial version** for clarity and correctness.  
- **Parallel version** using OpenMP for improved performance on multicore systems.  

---

## Output Structure

The solver generates the following files:

- `output_serial.dat` and `output_parallel.dat`  
  Columns:  
  1. \\( i \\): Node index along \\( x \\)  
  2. \\( j \\): Node index along \\( y \\)  
  3. \\( u_x \\): Velocity component in \\( x \\)-direction  
  4. \\( u_y \\): Velocity component in \\( y \\)-direction  
  5. \\( |U| \\): Resultant velocity magnitude  

- Validation files:  
  - `U_y=0.5_parallel.dat` → Velocity profile along the vertical midline (\\(x=0.5\\))  
  - `V_x=0.5_parallel.dat` → Velocity profile along the horizontal midline (\\(y=0.5\\))  

These profiles are compared against **analytical Couette flow solutions** for verification.

---

## Compilation & Execution

### Serial Implementation
```bash
g++ Couette_Flow_Serial.cpp -o couette_serial
./couette_serial
```
