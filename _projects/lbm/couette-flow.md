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


<figure style="text-align:center;">
  <img src="/images/self_upload/lbm/couette_ani.gif" 
       alt="Couette flow animation created using the values obtained from our code."
       style="width:95%; max-width:500px; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <figcaption style="margin-top:8px;">
    Couette flow animation created using the values obtained from our code.
  </figcaption>
</figure>


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

with \\(\rho\\) being the fluid density.

---

## Boundary Conditions

- **Bottom wall (y = 0):** Stationary wall (no-slip condition).  
- **Top wall (y = H):** Moves with constant velocity \\( U_0 \\) in the \\(x\\)-direction.  
- **Periodic in x:** Left and right boundaries are periodic to simulate an infinite channel.

The boundary conditions are treated a little differently in Lattice Boltzmann method than the conventional methods. 
---

## Implementation Details

- **Language:** C++  
<!-- - **Parallelization:** OpenMP   -->
- **Collision Model:** Single-relaxation-time BGK model  
- **Lattice Structure:** D2Q9 model  

<!-- Two implementations are provided:
- **Serial version** for clarity and correctness.  
- **Parallel version** using OpenMP for improved performance on multicore systems.   -->

---

## Output Structure

---

## Output Structure

When the solver is executed, it automatically creates an **output/** folder. Inside this folder, the code saves files named in the format: 
output_t<time>.dat

where `<time>` corresponds to the iteration number at which the data was written.  

Each file contains the following variables of interest at every lattice node:

1. **x-index (i)** – lattice index along the streamwise direction.  
2. **y-index (j)** – lattice index along the wall-normal direction.  
3. **uₓ** – x-velocity component.  
4. **uᵧ** – y-velocity component.  
5. **u** – resultant velocity magnitude.  
6. **ρ** – fluid density.  

These files are written at regular intervals (controlled by the parameter `save`) until the system reaches a steady state. The total number of iterations is user-controlled through the parameter `last_itr`. This flexibility allows the user to decide both **how long the simulation runs** and **at what intervals results are stored**.  

Additionally, a **test** file is generated at the end of the run. This file extracts velocity values along the vertical line (x = Lx – 2) of the domain. The values from this file can be compared against the **analytical solution of Couette flow** for validation purposes.  

---

## Compilation & Execution
```bash
g++ CouetteFlow.cpp -o couette
./couette_serial
```

## Results

On solving the Navier-Stokes equation and obtaining an expression for velocity profile in Couette flow, we get the following analytical solution:

$$
u_{y} = C_{1}y
$$

Where, \\C_{1}\\ is the constant and can be found out from the Boundary conditions (The velocity of the layer @ y=h). 
We compare the analytical solution to the Computed values below.

<div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/lbm/Validation_Couette.png" alt="Computed values compared with the values obtained from the analytical solution." style="width: 90%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Computed values compared with the values obtained from the analytical solution.
    </figcaption>
  </figure>
</div>

The fact that the values agree with each other, validates our code.

