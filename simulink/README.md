# Control Systems Engineering — Industrial Portfolio
### Week 1: Feedback Control & PID Design | Mechatronics Engineering · Universidad Nacional de Colombia

[![MATLAB](https://img.shields.io/badge/MATLAB-R2023b-orange?logo=mathworks)](https://www.mathworks.com)
[![Simulink](https://img.shields.io/badge/Simulink-Included-orange?logo=mathworks)](https://www.mathworks.com/products/simulink.html)
[![Control Toolbox](https://img.shields.io/badge/Control_Systems_Toolbox-Required-blue)](https://www.mathworks.com/products/control.html)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()

---

## Overview

This repository documents hands-on control systems design exercises developed during Week 1 of a structured industrial engineering portfolio. All problems are grounded in real-world applications — oil & gas drilling operations, Formula 1 active suspension, and robotic pick-and-place manufacturing — and solved using MATLAB/Simulink with full analytical derivation before simulation.

The work follows **Norman Nise's *Control Systems Engineering*** as the primary reference, extending each exercise beyond the textbook with industrial context and quantified performance validation.

**Target roles:** Control & Automation Engineer · Field Engineer (Oil & Gas) · Robotics Systems Engineer · Product Development Engineer

---

## Engineering Results at a Glance

| Exercise | System | Method | Key Result |
|---|---|---|---|
| Day 1 | Block diagram algebra | `feedback()`, `series()`, `pzmap()` | Closed-loop TF verified analytically and numerically |
| Day 2 | Pitch angle dynamics (aircraft) | 2nd-order approximation validity | Approximation **invalid** — zero at −0.435 only 3.84× from dominant poles (threshold: 5×) |
| Day 2 | Active suspension — F1 context | `stepinfo()`, damping analysis | ζ = 0.517, ωₙ = 1.93 rad/s → OS = 15%, tₛ = 4.06 s ✓ |
| Day 3 | Root locus — RHP zeros | `rlocus()`, Routh-Hurwitz | K_critical = 1.5 confirmed analytically and graphically |
| Day 3 | P controller — ABB IRB 1200 | Root locus design | K = 13.2 → OS reduced from 57% to 19.4%, tₛ from 7.31 s to 2.7 s |
| Day 4 | PD+I controller design | Angle/magnitude condition | Compensator zero at −65.99, K = 121.5 → meets %OS = 20%, Tp = 0.198 s |

---

## Project Structure

```
control-industrial-repaso/
│
├── README.md
├── matlab/
│   ├── Portafolio_week_1.mlx       ← Full LiveScript (open in MATLAB)
│   └── Portafolio_week_1.m         ← Exported plain script (readable on GitHub)
├── results/
│   └── Portafolio_week_1.pdf       ← Rendered LiveScript with all outputs
├── figures/
│   ├── day1_block_reduction.png
│   ├── day2_damping_comparison.png
│   ├── day2_f1_suspension.png
│   ├── day3_root_locus_rhp.png
│   ├── day3_abb_controller.png
│   └── day4_pid_design.png
└── simulink/
    └── (Simulink models — Week 2 onward)
```

---

## Day-by-Day Breakdown

### Day 1 — Transfer Functions & Block Diagram Algebra
**Industrial context:** Pressure control loop architecture in a wellbore model (Oil & Gas).

Derived closed-loop transfer functions manually for three systems of increasing complexity using Laplace algebra, then verified each result with MATLAB's `feedback()`, `series()`, and `parallel()` functions. Confirmed that a system without `s` dynamics produces no transient response — a key diagnostic insight when debugging flat step responses in field instrumentation.

**Functions used:** `tf()`, `zpk()`, `feedback()`, `pzmap()`, `step()`, `impulse()`, `bode()`

---

### Day 2 — Transient Response & Second-Order Approximation Validity
**Industrial context:** Pitch dynamics of a UAV / aircraft; active suspension design for motorsport.

**Key finding — approximation validity check:**
Analyzed a third-order system and applied Nise's criterion: the second-order approximation is valid only when neglected poles and zeros are at least **5× farther** from the imaginary axis than the dominant poles. Found that the zero at −0.435 was only **3.84×** away — approximation declared invalid. This type of analysis is standard practice in aerospace and automotive control validation.

```
Dominant poles:   s₁,₂ = −0.113 ± 0.0642i
Neglected zero:   s = −0.435  →  ratio = 3.84  (threshold: 5.0)  ✗ Invalid
```

**F1 active suspension — inverse design from specs:**

Given: %OS = 15%, tₛ = 4 s
Derived: ζ = 0.517, ωₙ = 1.93 rad/s
Verified: `stepinfo()` → OS = 15.0%, tₛ = 4.06 s ✓

```matlab
zeta = 0.5169;  wn = 1.9345;
sys = tf(wn^2, [1, 2*zeta*wn, wn^2]);
info = stepinfo(sys);
% Overshoot: 15.0%  |  SettlingTime: 4.06s
```

---

### Day 3 — Root Locus Design & P Controller for ABB IRB 1200
**Industrial context:** Position control of a rotational axis in an automated pick-and-place cell.

**Root locus with RHP zeros (Nise Exercise 8.5):**

```
G(s) = K(s − 2)(s − 4) / (s² + 6s + 25)
```

Both zeros in the Right Half Plane → system becomes unstable for K > 1.5. Validated analytically using Routh-Hurwitz:

```
Characteristic equation: s²(1+K) + s(6−4K) + (20K+8) = 0
Stability condition:  6 − 4K > 0  →  K < 1.5   ✓
```

**P controller design — ABB IRB 1200:**

```
Plant:  G(s) = 1 / [s(s+2)(s+8)]
Spec:   %OS = 5%  →  ζ = 0.69
```

| Metric | Open loop (K=1) | Designed (K=13.2) |
|---|---|---|
| Overshoot | 57.07% | 19.40% |
| Settling time | 7.31 s | 2.70 s |

Designed K limits overshoot and speeds up settling by **2.7×**. Noted that P-only control cannot eliminate steady-state error — motivates PID design in Day 4.

---

### Day 4 — PD+I Compensator Design (Nise Example 9.5)
**Industrial context:** High-precision motion control requiring zero steady-state error and specified transient response.

Full compensator design procedure:
1. Located desired closed-loop poles from specs (%OS = 20%, Tₚ = 2/3 original)
2. Applied **angle condition** to find compensator zero location
3. Applied **magnitude condition** to find gain K
4. Added integrator with zero at −0.1 for zero steady-state error

```
Desired poles:    s₁,₂ = −8.139 ± 15.887i
Angle deficiency: 18.41°
Compensator zero: s = −65.99
Gain K:           121.5
Peak time:        0.198 s  (target: 0.198 s ✓)
```

---

## Key Engineering Insights

**On second-order approximations:** Always verify the 5× distance criterion before simplifying a higher-order plant. An invalid approximation led to a 35% error in predicted overshoot in the aircraft pitch example — the kind of mistake that fails hardware validation.

**On RHP zeros:** They fundamentally limit achievable bandwidth and introduce non-minimum phase behavior. Any control system with RHP zeros has an upper bound on gain before instability — this is critical knowledge for designing drilling control systems where plant models often include transport delays.

**On P-only control:** A proportional controller alone cannot achieve zero steady-state error for type-0 plants. The Day 3 ABB example showed a 2.7× improvement in settling time with K tuning, but steady-state offset persists — the practical argument for integral action.

**On the angle/magnitude method:** More insightful than PID autotuning because it connects pole placement directly to time-domain specs. Forces the engineer to understand *why* a compensator zero is placed at a specific location, not just *where*.

---

## How to Run

**Requirements:** MATLAB R2022b or later · Control System Toolbox

```bash
# Clone the repository
git clone https://github.com/MarcosAlferoFierroSarria/control-industrial-repaso.git
cd control-industrial-repaso
```

Open `matlab/Portafolio_week_1.mlx` in MATLAB and run section by section, or run the exported `.m` script directly. Each day is clearly delimited with section headers.

Alternatively, open `results/Portafolio_week_1.pdf` to view all outputs without MATLAB.

---

## What's Next — Week 2

Signals & Systems: FFT analysis applied to real drilling data (ROP/WOB time series from SLB internship), filter design for noise removal in sensor signals, and spectral identification of dominant vibration frequencies in downhole tools.

---

## About

Marcos Fierro · Mechatronics Engineering · Universidad Nacional de Colombia  
Internship: SLB (Schlumberger) — Well Construction, Product Engineering · Jun–Dec 2025  
Developed DASCF/InfoBits: automated drilling performance analytics tools (Python + Power BI) — 97% reduction in manual reporting time.

[LinkedIn](https://linkedin.com/in/marcos-alfredo-fierro-sarria) · [GitHub](https://github.com/MarcosAlferoFierroSarria)
