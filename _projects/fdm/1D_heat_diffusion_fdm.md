---
title: "1D Heat Equation FDM"
layout: default
method: fdm
permalink: /projects/fdm/1D-heat-diffusion-fdm/
excerpt: "A simple implementation of the 1D heat equation using forward Euler method."
---

# 1D Steady-State Heat Diffusion Solver (C++ with OpenMP)

This project implements a **finite difference method (FDM)** solver for the **1D steady-state heat diffusion equation**.  
The code is written in **C++** and includes both a **serial implementation** and a **parallel implementation** using **OpenMP**, allowing for performance comparison between the two approaches.  

After execution, the solver generates an `output.dat` file with the following structure:  
- **Column 1:** Node index (\\(i = 1, 2, 3, \dots, L_x\\))  
- **Column 2:** Temperature values computed using the parallel implementation  
- **Column 3:** Temperature values computed using the serial implementation  
- **Column 4:** Relative difference (%) between the parallel and serial solutions  

Boundary conditions can be specified in the section marked `// initialization` within the code.  
The solution can be visualized using **Gnuplot** or any other plotting tool of choice.  

## Compilation & Execution
```bash
export OMP_NUM_THREADS=<number_of_threads>
g++ 1D_Heat_Diff.cpp -fopenmp -o heat_diff
./heat_diff
```

## Results 

<div style="display: flex; justify-content: space-around; gap: 20px;">

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/fdm/1D_heat_diffusion_1.png" 
         alt="The temperature profile when the boundary conditions at the start and end node are 100.0 and 1000.0 respectively" 
         style="max-width:100%; height:auto;">
    <figcaption>(fig. 1) The temperature profile when the boundary conditions at the start and end node are 100.0 and 1000.0 respectively.</figcaption>
  </figure>

  <figure style="flex: 1; text-align: center;">
    <img src="/images/self_upload/fdm/1D_heat_diffusion_2.png" 
         alt="The temperature profile when the boundary conditions at the start and end node are 100.0 and 100.0 respectively" 
         style="max-width:100%; height:auto;">
    <figcaption>(fig. 2) The temperature profile when the boundary conditions at the start and end node are 100.0 and 100.0 respectively.</figcaption>
  </figure>

</div>

In the above figures, fig. 1 and fig. 2, the temperature profile is linear which is consistent with the analytical solution and hence matches with the 1D steady state heat equation. Thus, validating our code.


