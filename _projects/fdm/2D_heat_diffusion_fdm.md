---
title: "2D Heat Equation FDM"
layout: default
method: fdm
permalink: /projects/fdm/2D-heat-diffusion-fdm/
excerpt: "A simple implementation of the 2D heat equation using forward Euler method."
---

<div style="padding-top: 5px;"></div>

# 2D Steady-State Heat Diffusion Solver (C++ with OpenMP)

This project implements a **finite difference method (FDM)** solver for the **2D steady-state heat diffusion equation**.  
The code can be found [**here in the GitHub link**](https://github.com/AdityaJaiswal17/Finite_Difference_Methods/blob/main/2D_HeatDiffusion_FDM/2D_heat_diff.cpp).

The solver is written in **C++** and provides both a **serial implementation** and a **parallel implementation** using **OpenMP**, enabling performance comparison for large-scale computations.  

After execution, the program generates two output files:  
- `output_parallel.dat` → Solution computed using the parallel implementation  
- `output_serial.dat` → Solution computed using the serial implementation  

Each file contains the following structure:  
- **Column 1:** Node index along the X-axis (\\(i = 1, 2, 3, \dots, L_x\\))  
- **Column 2:** Node index along the Y-axis (\\(j = 1, 2, 3, \dots, L_y\\))  
- **Column 3:** Computed temperature value at \\((i, j)\\)  
- **Column 4:** Temperature gradient in the \\(x\\)-direction (\\( \partial T / \partial x \\))  
- **Column 5:** Temperature gradient in the \\(y\\)-direction (\\( \partial T / \partial y \\))  

Boundary conditions can be specified in the section marked `// initialization` (line 61 & line 191 in the code).  
The results can be visualized using **Gnuplot** or other plotting tools.  

Boundary conditions in our [**source code**](https://github.com/AdityaJaiswal17/Finite_Difference_Methods/blob/main/2D_HeatDiffusion_FDM/2D_heat_diff.cpp) is set similar to the example/material in [**this website**](https://skill-lync.com/student-projects/solving-2d-heat-conduction-equation-using-various-iterative-solvers)

- **Temperature @ Top wall** = 600.0
- **Temperature @ Bottom wall** = 900.0
- **Temperature @ Left wall** = 400.0
- **Temperature @ Right wall** = 800.0

In addition to the above, all the walls are insulated as well.


---

## Mathematical Formulation

The governing equation for steady-state heat conduction in two dimensions is:

\[
\frac{\partial^2 T}{\partial x^2} + \frac{\partial^2 T}{\partial y^2} = 0
\]

where \\(T(x, y)\\) represents the temperature field.  

Using central finite differences, the discretized form at an interior grid point \\((i, j)\\) is:

\[
T_{i,j} = \frac{1}{2(1+\zeta)} \Big( T_{i+1,j} + T_{i-1,j} + \zeta \big(T_{i,j+1} + T_{i,j-1}\big) \Big)
\]

with

\[
\zeta = \left(\frac{\Delta x}{\Delta y}\right)^2
\]

This iterative relation is applied until the solution converges (\\(\text{error} < 10^{-7}\\)).  

The gradients are computed as:

\[
\frac{\partial T}{\partial x}\bigg|_{i,j} \approx \frac{T_{i+1,j} - T_{i-1,j}}{2 \Delta x}, 
\quad
\frac{\partial T}{\partial y}\bigg|_{i,j} \approx \frac{T_{i,j+1} - T_{i,j-1}}{2 \Delta y}
\]

---

## Example Visualization with Gnuplot

Surface/contour plot of temperature distribution:  
```gnuplot
set pm3d map
set size ratio 1.0   # adjust ratio as needed
splot 'output_serial.dat' using 1:2:3 with image
```

## Compilation & Execution
```bash
export OMP_NUM_THREADS=<number_of_threads>
g++ 2D_Heat_Diff.cpp -fopenmp -o heat_diff_2d
./heat_diff_2d
```

## Results

<figure style="display: flex; justify-content: center; gap: 20px; align-items: center;">
  <img src="/images/self_upload/fdm/2D_heat_diffusion_1.png" alt="2D Heat Diffusion Serial" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <img src="/images/self_upload/fdm/2D_heat_diffusion_2.jpg" alt="2D Heat Diffusion Parallel" style="width:45%; border:1px solid #ddd; border-radius:8px; padding:5px;">
  <figcaption style="text-align:center; margin-top:8px;">
    The temperature contour obtained with OUR code (left) compared to an example with the same conditions obtained using other softwares (right).
  </figcaption>
</figure>

The similarity between the contour plots obtained using OUR code and the one shown as an example [**here**](https://skill-lync.com/student-projects/solving-2d-heat-conduction-equation-using-various-iterative-solvers) valiates our code.
