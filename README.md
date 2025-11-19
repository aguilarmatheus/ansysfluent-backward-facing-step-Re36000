
# Backward-Facing Step – Turbulent Flow at Re = 36 000 (ANSYS Fluent)

This repository contains a canonical turbulent **backward-facing step (BFS)** test
case solved with **ANSYS Fluent** and used for **turbulence-model validation**
and **near-wall mesh design** (target y⁺ ≈ 1).

The geometry and operating conditions follow the classic backward-facing step
experiment of Driver and Seegmiller (1985), which provides detailed
measurements of wall shear stress and velocity profiles for validation.


---

## 1. Problem description

### Geometry

2D backward-facing step channel, extruded in the spanwise direction for meshing:

- Upstream channel height: **H₁ = 0.1016 m**
- Downstream channel height: **H₂ = 0.1140 m**
- Step height: **h = H₂ − H₁ = 0.0127 m**
- Upstream length (inlet to step): **L_in = 0.0508 m**
- Downstream length (step to outlet): **L_out = 0.508 m**
- Total length: **L ≈ 0.56 m**

For Fluent Meshing, the 2D profile is extruded in **z** over a span of **h / 4**
with **symmetry** boundary conditions applied on the lateral faces, to obtain a
quasi-2D configuration.

### Fluid and operating conditions

- Fluid: **air**, isothermal
- Density: **ρ = 1.18 kg/m³**
- Dynamic viscosity: **μ = 1.85 × 10⁻⁵ kg/(m·s)**
- Mean inlet velocity: **Ū = 41.7 m/s**
- Reynolds number based on step height:

  \[
  Re_h = \frac{\rho \, \overline{U} \, h}{\mu} \approx 36\,000
  \]

### Boundary conditions

- **Inlet:** fully developed turbulent channel profile for \(U\), \(k\) and
  \(ω/ε\) consistent with Re = 36 000 and the upstream height \(H₁\).
- **Outlet:** pressure outlet, gauge pressure = 0 Pa.
- **Walls (top and bottom):** no-slip, smooth.
- **Spanwise (z) faces:** symmetry.

---

## 2. Mesh and near-wall treatment

Meshes were generated with **Fluent Meshing** and designed to:

- Control near-wall resolution with a target **y⁺ ≈ 1** on all walls
  (low-Re SST k–ω).
- Guarantee good overall quality:
  - Minimum **orthogonal quality ≈ 0.43** (no cells below 0.30).
  - Smooth growth from wall-inflation to the core (growth rate ≤ 1.2).

Near-wall layers:

- First cell height computed from the y⁺ definition using
  \(y_1 \approx 7 × 10^{-6} \, \text{m}\) for y⁺ = 1 at Re = 36 000.
- Number of inflation layers: 22
- Growth rate: 1.2
- Total height: covers the boundary layer up to the outer shear layer.

Local refinement:

- Step corner region.
- Shear layer issuing from the step up to approximately **x ≈ 10 h**.
- Recirculation bubble and reattachment zone on the lower wall.

## 3. Turbulence modelling and solver settings

Primary model:

- **Turbulence model:** SST k–ω (low-Re formulation).
- **Wall treatment:** first cell in the viscous sublayer (y⁺).
- **Flow regime:** steady RANS;

Solver setup (Fluent):

- Pressure-based, steady.
- Coupled scheme for pressure–velocity coupling.
- Second-order spatial discretization for all equations.
- Body Force Weightedfor pressure.
- Convergence criteria:
  - Residuals < 1e−6 for all equations.
  - Mass imbalance ≈ 3.5×10⁻⁵ % of inlet mass flow (< 0.1 %).
  - `turb-out`: area-weighted average turbulent viscosity ratio at the outlet.
  - `wall-shear-report`: wall shear stress on the lower wall downstream of the step.

Automation (journal files) in [`fluent/`](fluent/):

- `bfs_sst_steady.jou`: steady SST k–ω run.




## 4. Validation metric

Model performance is assessed by comparing the **wall shear stress** on the
bottom wall downstream of the step with the experimental data of
Driver and Seegmiller (1985):

- Distribution of wall shear stress \( \tau_w(x) \) along the bottom wall.
- Depth and extent of the negative-shear region in the separation bubble.
- **Reattachment length** \( x_r/h \), obtained from the zero-crossing of
  \( \tau_w(x) \).



The corresponding comparison plot is stored in
[`results/figures/`](results/figures/):

- `wall_shear_sst_vs_experiment.png`



## 5. Appendix – First-layer height for y⁺ ≈ 1

The first-cell height is designed from the standard definition of y⁺:

\[
y^+ = \frac{\rho\,u_\tau\,y}{\mu}
\quad\Rightarrow\quad
y = \frac{y^+\,\mu}{\rho\,u_\tau}.
\]

The friction velocity is defined as:

\[
u_\tau = \sqrt{\frac{\tau_w}{\rho}},
\]

where the wall shear stress is estimated from a turbulent skin-friction
coefficient \(C_f\):

\[
\tau_w = \frac{1}{2} C_f \rho U^2.
\]

For a turbulent flat-plate–type correlation at \(Re_h = 36\,000\) we use:

\[
C_f = 0.079\,Re_h^{-0.25}.
\]

With

- \(\rho = 1.18\ \text{kg/m}^3\),
- \(\mu = 1.85\times10^{-5}\ \text{kg/(m·s)}\),
- \(U = 41.7\ \text{m/s}\),
- \(h = 0.0127\ \text{m}\),

we obtain \(Re_h \approx 36\,000\), \(C_f \approx 5.7\times10^{-3}\),
\(\tau_w \approx 5.9\ \text{Pa}\), \(u_\tau \approx 2.2\ \text{m/s}\).

For a target \(y^+=1\):

\[
y_1 = \frac{1\times\mu}{\rho\,u_\tau}
    \approx 7\times10^{-6}\ \text{m},
\]

which is the first-layer height used to design the mesh (target y⁺ ≈ 1).

## 6. References

- Driver, D. M., and Seegmiller, H. L., “Features of a Reattaching Turbulent Shear Layer in Divergent Channel Flow,”
  AIAA Journal, Vol. 23, No. 2, 1985, pp. 163–171. https://doi.org/10.2514/3.8890



