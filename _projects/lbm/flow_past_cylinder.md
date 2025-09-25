---
title: "Flow past cylinder LBM"
layout: default
method: lbm
permalink: /projects/lbm/flow-past-cylinder-lbm/
excerpt: "Modelling Flow past cylinder using Lattice boltzmann method with BGK."
---
<div style="padding-top: 5px;"></div>

# Flow Past a Cylinder (D2Q9 – BGK Model)

This case models the **incompressible viscous flow past a circular cylinder** using the **Lattice Boltzmann Method (LBM)**.  
The solver employs a **D2Q9 lattice** with the **BGK collision model**, at a default **Reynolds number of 100**.

You can find the full code here: [**Github Repository**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/blob/main/Flow_past_cylinder/flow_past_cylinder.cpp)

---

## Methodology

- The simulation is carried out on a **2D channel domain** with a cylinder of radius \( R = 25 \) placed near the inlet.  
- The governing parameters are:
  - **Inlet velocity:** \( U_\text{inlet} = 0.1 \)  
  - **Kinematic viscosity:** \( \nu = 0.05 \)  
  - **Relaxation time:** \( \tau = 3\nu + \tfrac{1}{2} \)  
- **Collision step:** Standard BGK model  
- **Streaming step:** Nearest-neighbor propagation on the D2Q9 lattice  

The solver also computes the **hydrodynamic forces** acting on the cylinder by evaluating the change in momentum flux across the obstacle boundary.

---

## Boundary Conditions

- **Inlet (left boundary):** Constant velocity profile  
- **Outlet (right boundary):** Zero-gradient condition  
- **Top & bottom boundaries:** Periodic boundary conditions  
- **Cylinder surface:** Bounce-back rule enforcing no-slip  

---

## Output Structure

Simulation results are stored in the **`output/`** folder at specified intervals.  
Each file contains:

1. \(x, y\) spatial coordinates  
2. \(u_x, u_y\) velocity components  
3. \(|\mathbf{u}|\): magnitude of velocity  
4. \(\rho\): fluid density  
5. \(F_x, F_y\): net hydrodynamic force on the cylinder  

For visualization, a helper script **`saveimage.sh`** converts the data files into contour plots. These images can be combined into animations to analyze the unsteady flow evolution.

---

## Results

<figure style="text-align:center;">
  <img src="/images/self_upload/flowPastCylinder.gif" alt="Flow past a cylinder simulated using LBM (D2Q9 lattice, BGK model)" style="width:80%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <figcaption>
    Flow past a cylinder at Reynolds number = <b>100</b>, simulated using the Lattice Boltzmann Method. 
  </figcaption>
</figure>

The simulation captures the key flow features associated with this problem:

- **Boundary layer separation** at the cylinder surface.  
- Formation of a **recirculation zone** immediately downstream.  
- Development of the **von Kármán vortex street**, characterized by periodic vortex shedding.  
- High velocity gradients near the cylinder and in the shear layers, clearly visible in the color contours.  
- **Time-varying hydrodynamic forces** on the cylinder due to alternating vortex shedding.  

This demonstrates the ability of LBM to reproduce **unsteady wake dynamics** and resolve complex flow structures around obstacles.
