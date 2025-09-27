---
title: "Poiseuille Flow (Gravity & Pressure Driven) — LBM"
layout: default
method: lbm
permalink: /projects/lbm/poiseuille-flow/
excerpt: "Poiseuille flow simulations using Lattice Boltzmann Method (LBM): gravity-driven and pressure-driven variants."
---

<div style="padding-top: 7px;"></div>

# Poiseuille Flow using Lattice Boltzmann Method (LBM)

This section presents the simulation of Poiseuille flow using the **D2Q9 Lattice BGK model**.  
Two different driving mechanisms are considered:  

1. **Gravity-driven Poiseuille flow** – Flow induced by a uniform body force using the **Guo forcing scheme**.  
2. **Pressure-driven Poiseuille flow** – Flow driven by an imposed density gradient at the inlet and outlet, representing a pressure difference.  

Both cases are solved with similar numerical formulations but differ in how the external forcing or boundary conditions are applied.

You can find the full code here (includes both gravity driven and pressure driven in their respective folders): [**Github Repository**](https://github.com/AdityaJaiswal17/Lattice_Boltzmann_Method/tree/main/PoisuelleFlow)

---

## Governing Equations

The lattice Boltzmann equation with the BGK approximation is used:

$$
f_i(\mathbf{x}+\mathbf{c}_i \Delta t, t + \Delta t) - f_i(\mathbf{x}, t) =
- \frac{1}{\tau}\left( f_i(\mathbf{x}, t) - f_i^{eq}(\mathbf{x}, t) \right) + F_i
$$

where:  
- \\( f_i \\) : particle distribution function in direction \(i\)  
- \\( f_i^{eq} \\) : equilibrium distribution  
- \\( \tau \\) : relaxation time, related to kinematic viscosity as  

$$
\nu = c_s^2 \left( \tau - \tfrac{1}{2} \right)
$$  

- \\( F_i \\) : external forcing term (used in the gravity-driven case)  
- \\( c_s^2 = \frac{1}{3} \\) : lattice speed of sound squared  

The macroscopic variables are recovered as:  

$$
\rho = \sum_i f_i, \quad
\mathbf{u} = \frac{1}{\rho} \sum_i f_i \mathbf{c}_i + \frac{\Delta t}{2} \mathbf{g}
$$

---

## 1. Gravity-Driven Poiseuille Flow

### Problem Description
The channel flow is driven by a **constant gravitational body force** applied in the \(x\)-direction.  
The forcing is introduced using the **Guo forcing scheme**:

$$
F_i = \left(1 - \tfrac{1}{2\tau}\right) w_i 
\left[
\frac{\mathbf{c}_i - \mathbf{u}}{c_s^2} +
\frac{(\mathbf{c}_i \cdot \mathbf{u})}{c_s^4}\mathbf{c}_i
\right] \cdot \rho \mathbf{g}
$$

where \\( \mathbf{g} = (g_x, 0) \\).

### Boundary Conditions
- **Top & Bottom walls**: No-slip (bounce-back scheme).  
- **Left & Right boundaries**: Periodic.  

### Code Implementation
- External gravity applied in the collision step.  
- The forcing scheme modifies the distribution functions directly.  

### Simulation Parameters
- Reynolds number: **Re = 100**  
- Domain size: \\(801 \times 81\\)  
- Gravity: \\( g_x = 1.25 \times 10^{-5} \\)  
- Kinematic viscosity: \\( \nu = 0.1 \\)  
- Maximum velocity: \\( u_{max} \approx 0.1 \\)  

### Results
The velocity profile converges to a parabolic distribution as expected for Poiseuille flow.  

**Animation:**  
<figure>
  <img src="/images/self_upload/lbm/gravity_driven.gif" alt="Gravity driven poiseuille flow created using the values obtained from our code.">
  <figcaption>Gravity driven poiseuille flow created using the values obtained from our code.</figcaption>
</figure>

---

## 2. Pressure-Driven Poiseuille Flow

### Problem Description
The channel flow is driven by a **pressure gradient** applied through a density difference at the inlet and outlet.  
This is equivalent to a body force but is introduced via **pressure (density) boundary conditions**.

### Boundary Conditions
- **Left wall**: Inlet pressure (higher density, \\( \rho = 1.0 + \Delta \rho/2 \\)).  
- **Right wall**: Outlet pressure (lower density, \\( \rho = 1.0 - \Delta \rho/2 \\)).  
- **Top & Bottom walls**: No-slip (bounce-back scheme).  

### Code Implementation
- Pressure gradient is introduced by modifying the density at inlet/outlet.  
- No explicit forcing term is used.  

### Simulation Parameters
- Reynolds number: **Re = 100**  
- Domain size: \\(501 \times 51\\)  
- Pressure difference (density difference): \\( \Delta \rho = 0.03 \\)  
- Kinematic viscosity: \\( \nu = 0.05 \\)  

### Results
The velocity profile matches the expected parabolic Poiseuille distribution.  

**Animation:**  
<figure>
  <img src="/images/self_upload/lbm/pressure_driven.gif" alt="Gravity driven poiseuille flow created using the values obtained from our code.">
  <figcaption>Gravity driven poiseuille flow created using the values obtained from our code.</figcaption>
</figure>

---

## Validation

Both cases were validated against the analytical Poiseuille velocity profile:  

### Gravity driven -
$$
u_x(y) = \frac{\rho g}{2 \mu} \, y (H - y), \quad u_{x,\text{max}} = \frac{\rho g H^2}{8 \mu}
$$


### Pressure driven -
$$
u(y) = \frac{1}{2\nu}\frac{\Delta P}{L} \left( h^2 - y^2 \right)
$$  

where \\(h\\) is half the channel height.  

**Validation Figures:**  
- Gravity-driven: 
  <div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/lbm/validation_poisuelleGrav.png" alt="Computed values compared with the values obtained from the analytical solution." style="width: 90%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Computed values compared with the values obtained from the analytical solution for <b> Gravity driven flow <b>.
    </figcaption>
  </figure>
</div>

- Pressure-driven: 
  <div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/lbm/validation_poisuelleFlow.png" alt="Computed values compared with the values obtained from the analytical solution." style="width: 90%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Computed values compared with the values obtained from the analytical solution for <b> Pressure driven flow <b>.
    </figcaption>
  </figure>
</div>

---

## Animations

- Gravity-driven Poiseuille flow @ **Re = 100**: *(Insert animation here)*  
- Pressure-driven Poiseuille flow @ **Re = 100**: *(Insert animation here)*  
- Additional case @ **Re = 500** (if applicable): *(Insert animation here)*  

---

## Conclusion
- Both driving mechanisms (gravity-driven using Guo forcing, and pressure-driven using density boundary conditions) produce the **expected parabolic velocity distribution**.  
- The **gravity-driven approach** directly incorporates the body force in the collision step.  
- The **pressure-driven approach** uses pressure (density) boundary conditions to mimic a gradient.  
- Both were validated against analytical solutions and showed excellent agreement.  

---
