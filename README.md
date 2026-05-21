# Mechatronic Engineering — Industrial Portfolio
## Week 1: Feedback Control & PID Design | Mechatronics Engineering · Universidad Nacional de Colombia

## Overview
This repository documents hands-on control systems design exercises developed during Week 1 (18/05/2026) of a structured industrial engineering portfolio (for now)

All problems are grounded in real-world applications like oil & gas drilling operations, motoring, and robotic pick-and-place manufacturing. These exercise had solved using MATLAB and Simulink with full analysis and their respective simulations.
This work follows Norman Nises's Control Systems Engineering as the primary reference. Some of the excercises had formulated using AI, extending each exercise beyond the textbook with industrial context and quantified performance validation.

Target roles:
Control and Automation Engineer, Robotics Systems Engineer, Mechatronics Systems Engineer, Field Engineer (Oil & Gas), Manufacturing Engineer, Toy Design Engineer, Automotive Engineer, and related roles.

## Engineering Results at a Glance for Week 1
| Exercise | System | Method | Key Result |
|---|---|---|---|
| Day 1 | Block diagram algebra | `feedback()`, `series()`, `pzmap()` | Closed-loop TF verified analytically and numerically |
| Day 2 | Pitch angle dynamics (aircraft) | 2nd-order approximation validity | Approximation **invalid** — zero at −0.435 only 3.84× from dominant poles (threshold: 5×) |
| Day 2 | Active suspension — F1 context | `stepinfo()`, damping analysis | ζ = 0.517, ωₙ = 1.93 rad/s → OS = 15%, tₛ = 4.06 s ✓ |
| Day 3 | Root locus — RHP zeros | `rlocus()`, Routh-Hurwitz | K_critical = 1.5 confirmed analytically and graphically |
| Day 3 | P controller — ABB IRB 1200 | Root locus design | K = 13.2 → OS reduced from 57% to 19.4%, tₛ from 7.31 s to 2.7 s |
| Day 4 | PD+I controller design | Angle/magnitude condition | Compensator zero at −65.99, K = 121.5 → meets %OS = 20%, Tp = 0.198 s |

## Project Structure
Currently, the repository has three folders: one for results ("results"), one for Matlab scripts ("Matlab"), and one for Simulink files ("Simulink"). The structure of this repository will likely change as progress is made.

The executive summary of the work done on the scripts, as well as the results obtained, can be found in this Readme file.

```
PORTAFOLIO-BY-MF/
├── README.md
├── matlab/
│   ├── Portafolio_week_1.mlx      (This will update at the end of the week) 
│   └── Portafolio_week_1.m         
├── results/
│   └── (Nothing for now)
├── figures/
│   └── (Nothing for now)
└── simulink/
    └── (Nothing for now)
```

## Activity by days
### Day 1: Transfer Functions & Block Diagram Algebra
A system was assembled using block diagrams, and an AI-provided exercise was developed in the Oil & Gas context.
**Functions used:** `tf()`, `zpk()`, `feedback()`, `pzmap()`, `step()`, `impulse()`, `bode()`

### Day 2: Transient Response & Second-Order Approximation Validity
**Industrial context:** Pitch dynamics of a UAV; active suspension design for motorsport.

**Key finding — approximation validity check:**
Analyzed a third-order system and applied Nise's criterion: the second-order approximation is valid only when we neglected poles and zeros are at least **5 times farther** from the imaginary axis than the dominant poles. Found that the zero at −0.435 was only **3.84 times farther** away, approximation declared invalid. This type of analysis is standard practice in aerospace and automotive control validation.

```
Dominant poles:   s₁,₂ = −0.113 ± 0.0642i
Neglected zero:   s = −0.435  →  ratio = 3.84  (threshold: 5.0)  ✗ Invalid
```

**F1 active suspension — inverse design from specs:**

Given: %OS = 15%, tₛ = 4 s
Derived: ζ = 0.517, ωₙ = 1.93 rad/s
Verified: `stepinfo()` with OS = 15.0%, tₛ = 4.06 s (We reach this objective)

```matlab
zeta = 0.5169;  wn = 1.9345;
sys = tf(wn^2, [1, 2*zeta*wn, wn^2]);
info = stepinfo(sys);
```

---

---

### Day 3: Root Locus Design & P Controller for ABB IRB 1200
**Industrial context:** Position control of a rotational axis in an automated pick-and-place robtics cell.

**Root locus with RHP zeros (Nise Exercise 8.5):**

```
G(s) = K(s − 2)(s − 4) / (s² + 6s + 25)
```

Both zeros in the Right Half Plane → system becomes unstable for K > 1.5. Validated analytically using Routh-Hurwitz:

```
Characteristic equation: s²(1+K) + s(6−4K) + (20K+8) = 0
Stability condition:  6 − 4K > 0  ;  K < 1.5 (We reach this)   
```

**P controller design — ABB IRB 1200:**

```
Plant:  G(s) = 1 / [s(s+2)(s+8)]
Spec:   %OS = 5% ; ζ = 0.69
```

| Metric | Open loop (K=1) | Designed (K=13.2) |
|---|---|---|
| Overshoot | 57.07% | 19.40% |
| Settling time | 7.31 s | 2.70 s |

Designed K limits overshoot and speeds up settling by **2.7**. Noted that controller based only in P constant cannot eliminate steady-state error, for this reason, this motivates PID design in another day.

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
Peak time:        0.198 s  (We reached the target)
```

---

## Key Engineering Insights for now:

**On second-order approximations:** Always verify the 5x distance criterion before simplifying a higher-order plant. An invalid approximation led to a 35% error in predicted overshoot in the aircraft pitch example, the kind of mistake that fails hardware validation. Although the approximation may not be exact, you can graph the response and see if the behavior is acceptable.

**On RHP zeros:** They fundamentally limit achievable bandwidth and introduce non-minimum phase behavior. Any control system with RHP zeros has an upper bound on gain before instability. This is a critical knowledge for designing drilling control systems where plant models often include transport delays.

**On P-only control:** A proportional controller alone cannot achieve zero steady-state error for grade 0 plants. The Day 3 ABB example showed a 2.7× improvement in settling time with K tuning, but steady-state offset persists, and that is the practical argument for integral action.

**On the angle/magnitude method:** More insightful than PID autotuning because it connects pole placement directly to time-domain specs. It forced to me for to understand *why* a compensator zero is placed at a specific location, not just *where*.


## How to Run

**Requirements:** MATLAB R2022b or later and intall Control System Toolbox
The Matlab Livescript, is not available until the end of the week.


## About

Marcos Fierro · Mechatronics Engineering · Universidad Nacional de Colombia  
Internship: SLB (Schlumberger) — Well Construction, Product Engineering · Jun–Dec 2025  
Developed DASCF/InfoBits: automated drilling performance analytics tools (Python + Power BI) — 97% reduction in manual reporting time.

[LinkedIn](https://linkedin.com/in/marcos-alfredo-fierro-sarria) · [GitHub](https://github.com/MarcosAlferoFierroSarria)







