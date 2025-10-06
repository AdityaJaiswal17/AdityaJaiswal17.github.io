---
layout: default
title: "Undergraduate Thesis"
permalink: /undergraduate-thesis/
author_profile: false
---

{% include base_path %}

<div style="padding-top: 7px;"></div>

# Optimal Recombination Algorithms for Generating Quad-Dominant Meshes

**Short description:** Algorithm that convert metric-adapted triangular meshes into quad-dominant meshes by scoring candidate triangle pairs with a combined aspect-ratio + angle-quality metric, recombining them greedily, and optionally smoothing. This work was done as a part of my Undergraduate Thesis for BITS Pilani at Indian Institute of Science [IISc].
The entire code repository can be found — [**here**](https://github.com/AdityaJaiswal17/BITS_Pilani-Undergraduate-Thesis). This code repo also has some `.vol` mesh files which can be used as example test cases.

Moreover, the thesis PDF can be found — [**here**](/files/Aditya_Jaiswal_Thesis.pdf) 

---

## 1 — Introduction
This thesis develops a practical recombination pipeline to convert metric-adapted triangular meshes (Netgen `.vol`) into quad-dominant (hybrid) meshes. Motivation and high-level goals:

- Triangular meshes are easy to adapt, but quads give better alignment with anisotropic flow and boundary layers and often reduce degrees of freedom needed for a target accuracy.  
- The thesis proposes a two-metric scoring for candidate quad formation (aspect ratio and angle quality), a greedy selection algorithm, boundary weighting, Laplacian smoothing, and an MPI variant for scaling.

This page summarizes the method, shows visual and numerical results, details how the code works, lists prerequisites, and provides reproducibility instructions for experiments used in the thesis.

---

## 2 — Work highlights

<div style="display: flex; flex-direction: column; align-items: center; width: 100%;">

  <!-- Row 1 -->
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 80%; text-align: center;">

    <div>
      <img src="/images/self_upload/thesis/cross_tri.png" alt="Quarter circle: initial tri mesh" style="width: 80%; margin: auto;">
      <p><em>Quarter circle: initial tri mesh</em></p>
    </div>

    <div>
      <img src="/images/self_upload/thesis/cross_recombined.png" alt="Quarter circle: recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Quarter circle: recombined quad mesh</em></p>
    </div>

  </div>

  <hr style="border: none; border-top: 2px dashed #999; width: 70%; margin: 2em 0;">

  <!-- Row 2 -->
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 80%; text-align: center;">

    <div>
      <img src="/images/self_upload/thesis/airofil_tri.png" alt="Diamond airfoil (zoomed out): initial tri mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed out): initial tri mesh</em></p>
    </div>

    <div>
      <img src="/images/self_upload/thesis/airfoil_recombined.png" alt="Diamond airfoil (zoomed out): recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed out): recombined quad mesh</em></p>
    </div>

  </div>

  <!-- Row 3 -->
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 80%; text-align: center;">

    <div>
      <img src="/images/self_upload/thesis/airfoil_tri_zoomedOut.png" alt="Diamond airfoil (zoomed in): initial tri mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed in): initial tri mesh</em></p>
    </div>

    <div>
      <img src="/images/self_upload/thesis/airfoil_recombined_zoomed.png" alt="Diamond airfoil (zoomed in): recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed in): recombined quad mesh</em></p>
    </div>

  </div>

  <hr style="border: none; border-top: 2px dashed #999; width: 70%; margin: 2em 0;">

  <!-- Row 4 -->
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 80%; text-align: center;">

    <div>
      <img src="/images/self_upload/thesis/config1_tri.png" alt="Configuration 1: initial tri mesh" style="width: 80%; margin: auto;">
      <p><em>Configuration 1: initial tri mesh</em></p>
    </div>

    <div>
      <img src="/images/self_upload/thesis/config1_recombined.png" alt="Configuration 1: recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Configuration 1: recombined quad mesh</em></p>
    </div>

  </div>

</div>


---

## 3 — Algorithm pipeline (step-by-step)
Below is the exact pipeline the code implements.

### 3.1 One-line pipeline
Read metric-adapted `.vol` → enumerate candidate triangle pairs → compute aspect-ratio & angle quality → combine with weight ω → sort candidates → greedy recombination → optional Laplacian smoothing → write new recombined `.vol`.

### 3.2 Detailed steps (screenshots taken from [**recombination.py**](https://github.com/AdityaJaiswal17/BITS_Pilani-Undergraduate-Thesis/blob/main/recombination.py) in the Github repo)

**Step 1 — Input:**  
- The algorithm expects a Netgen / NGSolve `.vol` file containing a triangular mesh adapted in metric space.

<figure style="display: flex; flex-direction: column; align-items: center; justify-content: center; width: 70%; margin: auto;">
  <img src="/images/self_upload/thesis/meshInput.png"
       alt="Mesh input block (snippet taken from the code)."
       style="width: 100%; height: auto; max-width: 400px; border-radius: 6px;">
  <figcaption style="text-align: center; font-style: italic; margin-top: 0.5em;">
    Mesh input block (snippet taken from the code).
  </figcaption>
</figure>

**Step 2 — Neighbor discovery:**  
- For each triangle, identify up to three neighboring triangles sharing an edge (opposite vertices). Implemented with `neighbors_elm` and `neighbors_vert` in the code.

<div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 24px; margin: 1.5em 0;">

  <figure style="flex: 1 1 400px; max-width: 45%; text-align: center; margin: 0;">
    <img src="/images/self_upload/thesis/notations.jpg" 
         alt="Triangle notation for identifying vertex and edges."
         style="width: 100%; height: auto; border-radius: 6px;">
    <figcaption style="text-align: center; font-style: italic; margin-top: 0.5em;">
      Triangle notation for identifying vertex and edges.
    </figcaption>
  </figure>

  <figure style="flex: 1 1 400px; max-width: 45%; text-align: center; margin: 0;">
    <img src="/images/self_upload/thesis/opp_neighbor_notation.jpg" 
         alt="Notations for identifying neighboring triangle."
         style="width: 100%; height: auto; border-radius: 6px;">
    <figcaption style="text-align: center; font-style: italic; margin-top: 0.5em;">
      Notations for identifying neighboring triangle.
    </figcaption>
  </figure>

</div>


In the above figures the **green triangle** is the current triangle. The vertices \[V_0, V_1, V_2\] are already defined as a set in the `.vol` mesh files. In the left figure, the blue triangles are the neighboring triangles and the side/edge opposite to each vertex is labelled as that numeric local edge (eg, The side opposite to the vertex \[V_0\] is labelled as 1 and so on.) In the right figure the blue triangle, that is the neighboring triangle has two sets of vertices common and the other vertex is the unique to the neighbor triangle and that is named as \[V^{4}_1\] which is the one straight opposite to the current green triangle's vertex \[V_1\].

**Step 3 — Two quality metrics (local):**  
- **Aspect ratio:** compute side lengths of the prospective quad and take `AR = max(side_i) / min(side_i)`.  
- **Angle quality:** compute four internal quad angles α_k and measure deviation from 90° (right angle). Convert deviation into a bounded quality metric in [0,1]. \[\left(max\left(1-\frac{2}{\pi}\max_k\left|\tfrac{\pi}{2}-\alpha_k\right|\right), 0\right)\]

<figure style="display: flex; flex-direction: column; align-items: center; width: 70%; margin: 2em auto;">
  <img src="/images/self_upload/thesis/AR_algoFlowchart.png" 
       alt="Algorithm to compute aspect ratios."
       style="width: 100%; height: auto; max-width: 450px; border-radius: 6px;">
  <figcaption style="text-align: center; font-style: italic; margin-top: 0.5em;">
    Algorithm to compute aspect ratios.
  </figcaption>
</figure>


**Step 4 — Normalization and linear combination:**  
- Normalize AR by the global maximum AR in the mesh (so AR ∈ [0,1] after normalization).  
- Combine:

\\[
q = \omega \cdot \frac{\text{AR}_{\text{local}}}{\max(\text{AR})} \;+\; (1-\omega)\left(max\left(1-\frac{2}{\pi}\max_k\left|\tfrac{\pi}{2}-\alpha_k\right|\right), 0\right)
\\]

**Step 5 — Collect & sort:**  
- Collect all candidate pairs into an `elm_info` array.  
- Sort descending by quality values.

**Step 6 — Greedy recombination:**  
- Iterate sorted list; whenever a candidate's two triangles are still active, accept the pairing, mark both triangles inactive (so they are not reused), and create a quad element record.

**Step 7 — Boundary handling:**  
- If a candidate lies on the domain boundary or adjacent to boundary nodes, multiply its score by a boundary factor (e.g., 1.5) to favor quads near walls.

**Step 8 — Smoothing:**  
- Optional Laplacian smoothing move averages of non-boundary vertices across their neighbors for `n` iterations to improve cell shapes.
- Using **laplacian smoothing** for the vertex smoothing. It computes the average nodal value of the connected neighboring nodes. For details and more, refer [**here**](https://www.andrew.cmu.edu/user/shimada/papers/00-imr-zhou.pdf)

**Step 9 — Output:**  
- Write recombined `.vol` files (one per experimental configuration) and optional PNG/SVG visualizations.

---

## 4 — Algorithm flowchart


<figure style="display: flex; flex-direction: column; align-items: center; width: 70%; margin: 2em auto;">
  <img src="/images/self_upload/thesis/program_flowchart.png" 
       alt="Program's algorithm and an overview."
       style="width: 100%; height: auto; max-width: 450px; border-radius: 6px;">
  <figcaption style="text-align: center; font-style: italic; margin-top: 0.5em;">
    Program's algorithm and an overview.
  </figcaption>
</figure>

## 5 — Results

### 5.1 Numerical summary (example test-case from thesis)

| Mesh       | # Triangles | # Quads |  DOF |    L2 error |
| ---------- | ----------: | ------: | ---: | ----------: |
| Initial    |         519 |       0 | 3114 | 2.48119e-05 |
| Recombined |          37 |     241 | 2391 |  4.2466e-04 |

**Interpretation:**

- The recombination reduces element counts and DOFs while keeping numerical error within an engineeringly acceptable range for the test case.

- The L2 error increased compared to the triangular solution, but the tradeoff is fewer DOFs and improved element alignment — often desirable in solver pipelines where DOF budget is constrained.

### 5.2 Visual results and commentary

From the Figures in section 2 under <i> work highlights <i>. Observe:

- Boundary-layer regions re-meshed with quad rows aligned tangentially.

- Reduced cell count in wake regions where triangles were combined into structured quads.

- Smoothing reduces corner skewness and improves internal angles.

### 5.3 Sensitivity

- The main sensitivity parameter is ω. Increasing ω favors lower AR quads; decreasing it favors angle alignment.

- Boundary weight (e.g., 1.5) strongly influences whether quads cascade along walls.

### 5.4 Performance & scaling

- The serial greedy algorithm scales linearly in the number of candidate pairs up to sorting cost (dominant O(N log N) for sorting).

## 6 — Code & function map (where to look in recombination.py)

- I/O / mesh read: meshFile = "mesh.vol" and Mesh(meshFile) via NGSolve/Netgen reader (loads vertices, elements).

- Neighbor detection: neighbors_elm(el_num), neighbors_vert(el_num).

- Angle computation: internal_angles.tri(...).

- Quality functions: quality_func_aspect_ratio(...), quality_func_angles(...).

- Main loop: elm_info array, greedy recombination.

- Boundary marking: is_boundary(...), boundary_weights(...).

- Smoothing: laplacian_smoothing(...).

## 7 — Prerequisites & installation

You need netgen and ngsolve packages to run the code sucessully and visualize the mesh.
To run the `.vol` mesh file format and to install the required libraries of ngsolve and netgen. Find the link to download (redirects to download link) [**here**](https://ngsolve.org/downloads).

## 8 — How to run (reproducibility & example commands)

```bash
python recombination.py 
```
OR (depending on your python verison, if you have python3)

```bash
python3 recombination.py
```

## 9 — Future work & extensions

- Use local search to improve global quad quality.

- Use angle based smoothing as it is more robust and accounts in various factors such as crossing over of the vertices as well.

- Metric-respecting smoothing.

- Extend to 3D prism/hexahedral recombination.

- Integrate solver feedback for adaptive recombination.

