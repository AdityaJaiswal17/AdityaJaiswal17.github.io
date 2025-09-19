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
    <img src="/images/self_upload/fdm/peclet_num.jpg" alt="Central differencing scheme vs Upwind scheme for Pe > 2" style="width: 70%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Central differencing scheme vs Upwind scheme for Pe &gt; 2.
    </figcaption>
  </figure>
</div>

So, in the code we set an **If statement** and check for the values of Peclet number at each node calculation and set the direction coefficients in the stream-vorticity equation accordingly.

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
g++ lid_driven_cavity_serial.cpp -o cavity_serial
./cavity_serial
```

### Parallel Version
```bash
export OMP_NUM_THREADS=<number_of_threads>
g++ lid_driven_cavity_parallel.cpp -o cavity_parallel
./cavity_parallel
```

### Gnuplot splot (if gnuplot is used for post processing data visualization -- for velocity solid contour plot)
```gnuplot
set pm3d map
set size ratio 1.0
sp '<output_file>.dat' using 1:2:7 with image
```
## Results

<figure style="display: flex; justify-content: center; gap: 20px; align-items: center;">
  <img src="/images/self_upload/fdm/validation_ux.png" alt="Computational vs experimental value for U_x=0.5" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <img src="/images/self_upload/fdm/validation_vy.png" alt="Computational vs experimental value for V_y=0.5" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <figcaption style="text-align:center; margin-top:8px;">
    Computed values of velocity obtained at midlines of the cavity U_x @ x=0.5 (left) and V_y @ y=0.5 (right) compared with the literature values from Ghia et al for the configuration when **Reynolds number = 100**.
  </figcaption>
</figure>

The above figure validates our code against literature values from [**Ghia et al**](http://www.msaidi.ir/upload/Ghia1982.pdf?i=1) that it gives authentic results.


<div style="display: flex; justify-content: space-around; gap: 20px;">

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/fdm/lid_driven_fdm_re50.png" 
         alt="Velocity contours for the configuration when RE = 50" 
         style="max-width:100%; height:auto;">
    <figcaption>Velocity contours for the configuration when RE = 50.</figcaption>
  </figure>

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/fdm/lid_driven_fdm_re100.png" 
         alt="Velocity contours for the configuration when RE = 100" 
         style="max-width:100%; height:auto;">
    <figcaption>Velocity contours for the configuration when RE = 100.</figcaption>
  </figure>

</div>

<div style="text-align: center; margin: 15px 0;">
  <figure style="display: inline-block; margin: 0;">
    <img src="/images/self_upload/fdm/lid_driven_fdm_re200.png" alt="Velocity contours for the configuration when RE = 200." style="width: 80%; height: auto; border-radius: 8px;">
    <figcaption style="font-size: 0.9em; margin-top: 5px;">
      Velocity contours for the configuration when RE = 200. &gt; 2.
    </figcaption>
  </figure>
</div>

The above figures show the velocity contours at steady state of the Lid driven cavity for varying Reynolds numbers. Furthermore, the location and displacement of central (primary) vortex also agrees with the theory.