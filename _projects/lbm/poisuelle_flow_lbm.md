---
title: "Poiseuille Flow (Gravity & Pressure Driven) — LBM"
layout: default
method: lbm
permalink: /projects/lbm/poiseuille-flow/
excerpt: "Poiseuille flow simulations using Lattice Boltzmann Method (LBM): gravity-driven and pressure-driven variants."
---

<div style="padding-top: 7px;"></div>

# Poiseuille Flow using Lattice Boltzmann Method (C++ with OpenMP)

This project implements **Poiseuille flow** (channel flow between two parallel plates) using the **Lattice Boltzmann Method (LBM)** with the **D2Q9 BGK scheme**.  
Two different driving mechanisms are considered:  

1. **Gravity-driven Poiseuille Flow** (body force driven)  
2. **Pressure-driven Poiseuille Flow** (density/pressure gradient)  

The solver is written in **C++** with both **serial** and **parallel (OpenMP)** implementations.  
The full code and results are available in the [**GitHub repository**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/tree/main/PoisuelleFlow).  

---

## Methodology

The general **LBM evolution equation** is:  

$$
f_i(\mathbf{x}+\mathbf{c}_i \Delta t,\, t+\Delta t) - f_i(\mathbf{x}, t)
= -\frac{1}{\tau} \left( f_i(\mathbf{x},t) - f_i^{eq}(\mathbf{x},t) \right)
$$  

with equilibrium distribution:  

$$
f_i^{eq} = w_i \rho \left[ 1 + \frac{\mathbf{c}_i \cdot \mathbf{u}}{c_s^2}
+ \frac{(\mathbf{c}_i \cdot \mathbf{u})^2}{2c_s^4}
- \frac{|\mathbf{u}|^2}{2c_s^2} \right]
$$  

where:  
- \(f_i\): particle distribution function  
- \(\mathbf{c}_i\): lattice velocities (D2Q9)  
- \(\tau\): relaxation time  
- \(w_i\): lattice weights  
- \(c_s\): speed of sound in lattice  

Macroscopic quantities:  

$$
\rho = \sum_i f_i, \quad 
\mathbf{u} = \frac{\sum_i f_i \mathbf{c}_i}{\rho}
$$  

---

### Case A: Gravity-Driven Poiseuille Flow  

- Flow is driven by a **constant body force** (gravity-like) along the streamwise direction.  
- Boundary conditions:  
  - **Top & bottom walls** → bounce-back (no-slip)  
  - **Streamwise direction** → periodic boundaries  
- Analytical solution:  
  $$
  u(y) = \frac{G}{2\nu} \, y (H - y)
  $$  

---

### Case B: Pressure-Driven Poiseuille Flow  

- Flow is driven by a **pressure/density gradient** between inlet and outlet.  
- Boundary conditions:  
  - **Top & bottom walls** → bounce-back (no-slip)  
  - **Inlet & outlet** → density/pressure-based BCs  
- Analytical solution:  
  $$
  u(y) = \frac{1}{2\mu} \, \frac{\Delta p}{L} \, y (H - y)
  $$  

---

## Output Structure  

Each solver generates the following files:  

- `velocity_profile.dat` → velocity distribution across channel  
- `validation.png` → computed vs analytical solution  
- `contour.png` → steady-state velocity contour  
- (optional) GIF animations → time evolution of velocity field  

---

## Compilation & Execution  

### Serial (Gravity-Driven)  
```bash
g++ gravity_driven_serial.cpp -o poiseuille_gravity_serial
./poiseuille_gravity_serial
