---
title: "Lid driven cavity FDM"
layout: default
method: fdm
permalink: /projects/fdm/lid-driven-cavity-fdm/
excerpt: "Lid driven cavity implementation using stream vorticity formulation."
---
<div style="padding-top: 7px;"></div>

# Lid-Driven Cavity Solver (C++ with OpenMP)

This project implements a **finite difference method (FDM)** solver for the **2D incompressible Navier–Stokes equations** in a **lid-driven cavity flow** configuration. The implementation follows the **streamfunction–vorticity formulation** of the governing equations, mentioned in [IIST, 2010](https://old.iist.ac.in/sites/default/files/people/psi-omega.pdf). The above mentioned PDF also talks about discretization of the equations as well as the schemes used.

The code can be found [**here on GitHub**](https://github.com/AdityaJaiswal17/Finite_Difference_Methods/tree/main/Lid_Driven_Cavity).  

The solver is written in **C++** and provides both:  
- a **serial implementation**, and  
- a **parallel implementation** using **OpenMP**  

This allows performance comparison between the two approaches, while ensuring the correctness of the solution.  

---

## Methodology  

The solver implements the **streamfunction–vorticity formulation** of the 2D Navier–Stokes equations, following the approach described by IIST:  

- **Vorticity transport equation:**  
$$
\frac{\partial \omega}{\partial t} + u \frac{\partial \omega}{\partial x} + v \frac{\partial \omega}{\partial y}
= \frac{1}{Re} \left( \frac{\partial^2 \omega}{\partial x^2} + \frac{\partial^2 \omega}{\partial y^2} \right)
$$  

- **Streamfunction relation:**  
$$
\nabla^2 \psi = -\omega
$$  

- **Velocity components from streamfunction:**  
$$
u = \frac{\partial \psi}{\partial y}, 
\quad 
v = -\frac{\partial \psi}{\partial x}
$$  

Boundary conditions are applied at the cavity walls:  
- **Top wall (lid):** constant velocity in the \\(x\\)-direction.  
- **Other walls:** no-slip condition.  

Also, during discretization, instead of using Central Differencing scheme we use Upwind scheme. The reason for this choice is that the Central differencing scheme does not account or capture the convective terms accurately (does not represent the directional influence of a disturbance). Recalling a simple lecture from Computational fluid dynamics, **Peclet number** is the ratio of advection to the diffusion. 
\\[Pe = \frac{U L}{\Gamma}\\]
The above expression relates how advection and diffusion relate to peclet number. Now, if, Pe > 2 then Upwind scheme is used as Central differencing scheme would be unstable. The figure below draws out the comparison of the same.

<div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/fdm/peclet_num.jpg" alt="Central differencing scheme vs Upwind scheme for Pe > 2" style="width: 50%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Central differencing scheme vs Upwind scheme for Pe &gt; 2.
    </figcaption>
  </figure>
</div>

So, in the code we set an **If statement** and check for the values of Peclet number at each node calculation and set the coefficients accordingly.

---

## Output Structure  

After execution, the solver generates the following files:  

### 1. **Full Field Data**  
- `output_parallel.dat` (parallel run)  
- `output_serial.dat` (serial run)  

Each row corresponds to a grid point in the domain, with the following structure:  

| Column | Description |  
|--------|-------------|  
| 1 | Node index in the \\(x\\)-direction (\\(i\\)) |  
| 2 | Node index in the \\(y\\)-direction (\\(j\\)) |  
| 3 | Streamfunction (\\(\psi\\)) |  
| 4 | Vorticity (\\(\omega\\)) |  
| 5 | \\(x\\)-velocity component (\\(u\\)) |  
| 6 | \\(y\\)-velocity component (\\(v\\)) |  
| 7 | Resultant velocity magnitude (\\(\sqrt{u^2+v^2}\\)) |  

### 2. **Validation Data (Midline Profiles)**  
For verification against benchmark results from **Ghia et al. (1982, Re=100)**:  

- `U-Re_x=0.5_parallel.dat`: \\(u\\)-velocity along the vertical centerline (\\(x=0.5\\))  
- `V-Re_x=0.5_parallel.dat`: \\(v\\)-velocity along the horizontal centerline (\\(y=0.5\\))  

These files allow comparison of the computed midline velocities with literature values.  

---

## Compilation & Execution  

### Serial Version  
```bash
g++ Lid_Driven_Cavity.cpp -o cavity_serial
./cavity_serial
```