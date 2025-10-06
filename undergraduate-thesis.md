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
Moreover, The thesis PDF can be found — [**here**](/files/Aditya_Jaiswal_Thesis.pdf) 

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
      <img src="/images/self_upload/thesis/airfoil_tri_zoomedOut.png" alt="Diamond airfoil (zoomed in): initial tri mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed in): initial tri mesh</em></p>
    </div>

  </div>

  <hr style="border: none; border-top: 2px dashed #999; width: 70%; margin: 2em 0;">

  <!-- Row 3 -->
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 80%; text-align: center;">

    <div>
      <img src="/images/self_upload/thesis/airfoil_recombined.png" alt="Diamond airfoil (zoomed out): recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed out): recombined quad mesh</em></p>
    </div>

    <div>
      <img src="/images/self_upload/thesis/airfoil_recombined_zoomed.png" alt="Diamond airfoil (zoomed in): recombined quad mesh" style="width: 80%; margin: auto;">
      <p><em>Diamond airfoil (zoomed in): recombined quad mesh</em></p>
    </div>

  </div>

</div>


---

## 3 — Algorithm pipeline (step-by-step)
Below is the exact pipeline the code implements. This is written to be both human-readable and reproducible.

### 3.1 One-line pipeline
Read metric-adapted `.vol` → enumerate candidate triangle pairs → compute aspect-ratio & angle quality → combine with weight ω → sort candidates → greedy recombination → optional Laplacian smoothing → write new `.vol`.

### 3.2 Detailed steps

**Step 1 — Input:**  
- The tool expects a Netgen / NGSolve `.vol` file containing a triangular mesh adapted in metric space.

**Step 2 — Neighbor discovery:**  
- For each triangle, identify up to three neighboring triangles sharing an edge (opposite vertices). Implemented with `neighbors_elm` and `neighbors_vert` in the code.

**Step 3 — Two quality metrics (local):**  
- **Aspect ratio (AR):** compute side lengths of the prospective quad and take `AR = max(side_i) / min(side_i)`.  
- **Angle quality (AngleQual):** compute four internal quad angles α_k and measure deviation from 90° (right angle). Convert deviation into a bounded quality metric in [0,1].

**Step 4 — Normalization and linear combination:**  
- Normalize AR by the global maximum AR in the mesh (so AR ∈ [0,1] after normalization).  
- Combine:

\\[
q = \omega \cdot \frac{\text{AR}_{\text{local}}}{\max(\text{AR})} \;+\; (1-\omega)\left(1-\frac{2}{\pi}\max_k\left|\tfrac{\pi}{2}-\alpha_k\right|\right)
\\]

**Step 5 — Collect & sort:**  
- Collect all candidate pairs into an `elm_info` array with fields (element_id, neighbor_id, edge_index, q, active_flag).  
- Sort descending by `q`.

**Step 6 — Greedy recombination:**  
- Iterate sorted list; whenever a candidate's two triangles are still active, accept the pairing, mark both triangles inactive (so they are not reused), and create a quad element record.

**Step 7 — Boundary handling:**  
- If a candidate lies on the domain boundary or adjacent to boundary nodes, multiply its score by a boundary factor (e.g., 1.5) to favor quads near walls.

**Step 8 — Smoothing:**  
- Optional Laplacian smoothing move averages of non-boundary vertices across their neighbors for `n` iterations to improve cell shapes.

**Step 9 — Output:**  
- Write recombined `.vol` files (one per experimental configuration) and optional PNG/SVG visualizations.

---

## 4 — Flowchart (visual algorithm)
**Preferred:** replace this with **Figure 4.4 (Flowchart)** from the thesis PDF (crop/upload to `flowchart_fig4_4.png` or `flowchart.svg`).  
**Otherwise**, the Mermaid block below is a readable text diagram (works if your site renders Mermaid).

```mermaid
flowchart TD
  A[Input: metric-adapted .vol] --> B[Neighbor discovery]
  B --> C[Compute AR + Angle Quality]
  C --> D[Normalize & combine: q = ω*ARnorm + (1-ω)*AngleQual]
  D --> E[Collect & Sort candidates by q]
  E --> F[Greedy recombination (mark used triangles OFF)]
  F --> G[Optional: Laplacian smoothing]
  G --> H[Write recombined .vol and stats/images]
```

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

From the Figures 5.1–5.4 and 5.11–5.12 (placeholders above). Observe:

- Boundary-layer regions re-meshed with quad rows aligned tangentially.

- Reduced cell count in wake regions where triangles were combined into structured quads.

- Smoothing reduces corner skewness and improves internal angles.

### 5.3 Sensitivity

- The main sensitivity parameter is ω. Increasing ω favors lower AR quads; decreasing it favors angle alignment.

- Boundary weight (e.g., 1.5) strongly influences whether quads cascade along walls.

### 5.4 Performance & scaling

- The serial greedy algorithm scales linearly in the number of candidate pairs up to sorting cost (dominant O(N log N) for sorting).

- The MPI variant parallelizes candidate evaluation and can improve wall-clock time for large meshes.

## 6 — Code & function map (where to look in recombination.py)

- I/O / mesh read: Mesh(meshFile) via NGSolve/Netgen reader (loads vertices, elements).

- Neighbor detection: neighbors_elm(el_num), neighbors_vert(el_num).

- Angle computation: internal_angles.tri(...).

- Quality functions: quality_func_aspect_ratio(...), quality_func_angles(...).

- Main loop: elm_info array, greedy recombination.

- Boundary marking: is_boundary(...), boundary_weights(...).

- Smoothing: laplacian_smoothing(...).

- MPI variant: if mpi_enabled: ...

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

- Metric-respecting smoothing.

- Extend to 3D prism/hexahedral recombination.

- Integrate solver feedback for adaptive recombination.

