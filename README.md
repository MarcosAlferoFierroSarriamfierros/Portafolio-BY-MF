# Marcos Fierro: Universidad Nacional de Colombia's Mechatronic Engineer 
# Industrial Portfolio
## Week 1: Feedback Control & PID Design   

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
| Day 2 | Pitch angle dynamics (aircraft) | 2nd-order approximation validity | Approximation **invalid** — zero at −0.435 only 3.84X from dominant poles (threshold: 5x) |
| Day 2 | Active suspension: F1 context | `stepinfo()`, damping analysis | ζ = 0.517, ωₙ = 1.93 rad/s ; OS = 15%, tₛ = 4.06 s |
| Day 3 | Root locus:RHP zeros | `rlocus()`, Routh-Hurwitz | K_critical = 1.5 confirmed analytically and graphically |
| Day 3 | P controller: ABB IRB 1200 | Root locus design | K = 13.2; OS reduced from 57% to 19.4%, tₛ from 7.31 s to 2.7 s |
| Day 4A | PD+I compensator — 3rd order system (Nise 9.5) | Angle + magnitude condition | Zero at −65.99, K = 121.5 → Tₚ = 0.17 s, OS < 20% ✓ |
| Day 4B | PID — translational mass-spring-damper | Root locus pole placement | %OS = 8%, tₛ = 2 s specs met. Derivative zero validity confirmed (>5× rule ✓) |
| Day 4C | PID vs Ziegler-Nichols — drilling mud flow control (dead time) | Padé + LGR vs Z-N | Z-N: Kcr = 6.54, Pcr = 1.45 s. LGR: ζ = 0.707, tₛ < 3 s. Both compared on same plot |
| Day 5A | Top Drive speed control — PI design by pole-zero cancellation | Bode + frequency domain | Kp = 1.0, Ti = τ = 0.8 s → wcp = 15 rad/s, PM ≥ 45° ✓ |
| Day 5B | Satellite antenna azimuth control (Nise Ch.10 Case Study) | Bode stability analysis | K_critical = 2623.3 (analytical + verified). K=30 → GM = 38.83 dB, PM = 59.02° |
| Day 6 | Oil/water separator level control — full design project | Linearization → pidtune → I-P scheme | GM = ∞, PM = 76.36°. tₛ = 875 s, OS = 0% ✓. I-P selected over PI to eliminate zero-induced overshoot |
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
Neglected zero:   s = −0.435  ;  ratio = 3.84  (threshold: 5.0)  (And for this, it's not rigth)
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

Both zeros in the Right Half Plane, that's why the system becomes unstable for K > 1.5. Validated analytically using Routh-Hurwitz:

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

**Case A — PD+I compensator for a 3rd-order plant (Nise Example 9.5)**

Full systematic design using the angle and magnitude conditions:

1. Located desired poles from specs: %OS = 20%, Tₚ = 2/3 original
2. Computed angle deficiency: **18.41°** → placed compensator zero at **−65.99**
3. Applied magnitude condition → **K = 121.5**
4. Added integrator with zero at −0.1 for zero steady-state error
```
Desired poles:    s₁,₂ = −8.139 ± 15.887i
Angle deficiency: 18.41°
Compensator zero: s = −65.99
Gain K:           121.5
Peak time:        0.198 s  (We reached the target)
```
**Case B — PID for a translational mass-spring-damper (robotics lab)**

Plant: G(s) = 1/(s² + 0.5s + 5) · Specs: %OS ≤ 8%, tₛ ≤ 2 s, ess = 0

Key design steps: roots found at −0.25 ± 2.222i. Angle condition used to place
derivative zero. Integral zero placed at −0.25 (pole-zero proximity strategy
to avoid slow mode trapping — standard practice in embedded motion control).

```matlab
% Verified specs
info_control_sys_act2_d4.Overshoot     % ≤ 8%
info_control_sys_act2_d4.SettlingTime  % ≤ 2 s
```

**Case C — PID with dead time: drilling mud flow control (Oil & Gas)**

Most industrially relevant exercise of the week. Real drilling systems exhibit
transport delay between the control valve signal and the measured flow rate.
---
| Method | Kcr / Design basis | %OS | tₛ | Notes |
|---|---|---|---|---|
| Ziegler-Nichols | Kcr = 6.54, Pcr = 1.45 s | Higher | ~15 s | Fast to apply, aggressive |
| Root Locus (LGR) | ζ = 0.707, tₛ < 3 s | Controlled | < 3 s | More robust, slower design |

**Practical insight:** Z-N tends to over-tune for processes with dead time —
the LGR approach gives explicit control over damping, which is why it's
preferred in critical Oil & Gas applications where oscillation causes wellbore
instability.

---

### Day 5 — Frequency Domain Analysis & Bode-Based Design (Two Industrial Cases)

**Case A — Top Drive speed control: PI design by pole-zero cancellation**

**Industrial context:** Stick-slip in rotary drilling is one of the leading
causes of drillstring failure. Precise RPM control of the Top Drive prevents
torsional resonance and optimizes ROP.

Plant: G(s) = 12 / (0.8s + 1)   [Km = 12, τ = 0.8 s]

Design strategy: set Ti = τ = 0.8 s to cancel the plant pole, then choose
gain crossover frequency wcp = 15 rad/s as a speed/noise tradeoff.
**Key finding:** A first-order plant with PI controller and pole-zero
cancellation reduces to an integrator in closed loop — guaranteed zero
steady-state error with tunable bandwidth. Verified with `margin()` and
`stepinfo()`.

**Case B — Satellite antenna azimuth positioning (Nise Ch. 10 Case Study)**

Plant: G(s) = 6.63 / [s(s + 1.71)(s + 100)]

| Analysis | Result |
|---|---|
| Analytical K_critical | **2623.3** (symbolic derivation via `solve()`) |
| Verified by `margin()` | 2623.3 ✓ |
| Operating point (K = 30): GM | **38.83 dB** |
| Operating point (K = 30): PM | **59.02°** |
| Gain crossover frequency | 1.00 rad/s |
| Phase crossover frequency | 13.08 rad/s |
| Predicted %OS (from PM) | ~14.5% |
| Predicted tₛ (from BW) | Estimated via ωn from bandwidth relation |

**Notable technique:** Phase crossover frequency computed analytically using
`syms` and `solve()` — solving −180° = −90° − arctan(ω/1.71) − arctan(ω/100).
This symbolic approach is more rigorous than reading from the Bode plot and
mirrors what a controls engineer does when validating a design without
hardware access.

```matlab
% Analytical critical gain — key lines
syms x
ang_eq = -180 == -90 - atand(x/1.71) - atand(x/100);
w_pc = double(solve(ang_eq, x));   % phase crossover frequency

syms Kcr
gain_eq = 1 == Kcr * (6.63 / (w_pc * sqrt(w_pc^2+1.71^2) * sqrt(w_pc^2+100^2)));
K_critical = double(solve(gain_eq, Kcr));   % = 2623.3
```
### Day 6 — Capstone Project: Oil/Water Separator Level Control (Full Design Cycle)

**Industrial context:** In surface production facilities, the water-crude
interface level in a three-phase separator is safety-critical. High level →
water carryover into crude (damages refineries). Low level → oil carryunder
into produced water (environmental violation). This project replicates the
full engineering workflow for commissioning a real control loop.

**Plant derivation — symbolic linearization:**

The nonlinear mass balance dh/dt = (Qi − Cv√h) / A was linearized using
`jacobian()` from the Symbolic Math Toolbox around the operating point h₀ = 2 m:

```matlab
syms A Qi Cv h h0
f = (Qi - Cv*sqrt(h)) / A;
a_sym = jacobian(f, h);   % ∂f/∂h  →  −Cv/(2A√h)
b_sym = jacobian(f, Qi);  % ∂f/∂Qi →   1/A
```

Evaluated at: A = 4.91 m², Cv = 7.57×10⁻³, h₀ = 2 m

**Three-stage controller design — iterative industrial process:**

| Stage | Method | tₛ result | %OS | Decision |
|---|---|---|---|---|
| 1 | `pidtune()` default | > 5800 s | 0% | Too slow — rejected |
| 2 | `pidtune()` with wc = 4/tₛ | ~93 s | > 0% | Faster but overshoot — rejected |
| 3 | Analytical PI (pole placement at ζ=1) | ~600 s target | 11.36% | Zero in TF causes overshoot — rejected |
| **4** | **I-P scheme** | **875 s** | **0% ✓** | **Selected** |

**Why I-P instead of PI — the key engineering decision:**

A standard PI controller places a zero in the closed-loop transfer function.
That zero accelerates the response but inevitably introduces overshoot — even
with ζ = 1 pole placement. In separator level control, any overshoot means
either water carryover or oil carryunder. Neither is acceptable.

The I-P (Integral-Proportional) scheme restructures the loop so that
proportional action acts on the **measured variable**, not the error signal.
This eliminates the numerator zero and produces a monotonic, overdamped
response — at the cost of a slower settling time.

```matlab
sys_inner = feedback(tf_proyecto, k_p_proyecto);   % proportional on measurement
C_integral = tf(k_i_proyecto, [1 0]);              % pure integrator on error
sys_IP = feedback(C_integral * sys_inner, 1);
% stepinfo: OS = 0%, tₛ = 875 s
```

**Settling time discrepancy — honest engineering analysis:**

The target was tₛ = 600 s, achieved was 875 s. The discrepancy is explained
and accepted: the tₛ = 4/(ζωn) formula assumes a standard second-order system
with the zero present. By removing the zero (I-P), the formula no longer
applies — the actual response is slower by design. In a real separator, 875 s
is within the 13–15 minute industry standard for interface level loops.

**Stability and robustness — frequency analysis:**
```matlab
sys_inner = feedback(tf_proyecto, k_p_proyecto);   % proportional on measurement
C_integral = tf(k_i_proyecto, [1 0]);              % pure integrator on error
sys_IP = feedback(C_integral * sys_inner, 1);
% stepinfo: OS = 0%, tₛ = 875 s
```

**Settling time discrepancy — honest engineering analysis:**

The target was tₛ = 600 s, achieved was 875 s. The discrepancy is explained
and accepted: the tₛ = 4/(ζωn) formula assumes a standard second-order system
with the zero present. By removing the zero (I-P), the formula no longer
applies — the actual response is slower by design. In a real separator, 875 s
is within the 13–15 minute industry standard for interface level loops.

**Stability and robustness — frequency analysis:**

## Key Engineering Insights

**On second-order approximations:** Always verify the 5× distance criterion
before simplifying a higher-order plant. In the Day 2 aircraft pitch example,
an invalid approximation produced a 35% error in predicted overshoot. In Day 4A,
the derivative zero at −65.99 satisfied the criterion — making the approximation
valid and the design trustworthy.

**On RHP zeros:** They impose a fundamental upper bound on achievable gain.
The Day 3 system became unstable at K = 1.5 — confirmed both by Routh-Hurwitz
and root locus. This class of plants appears in drilling systems with
recirculation paths and requires bounded-gain controllers.

**On dead time in process control:** The Day 4C mud flow exercise showed that
Ziegler-Nichols over-tunes processes with transport delay. In Oil & Gas, where
wellbore instability is catastrophic, LGR-based design with explicit damping
control is the safer and more defensible approach.

**On pole-zero cancellation (Day 5A):** Valid when the cancelled pole is
stable and not close to the imaginary axis. Setting Ti = τ eliminates the
plant lag and gives the closed-loop system first-order behavior — simple,
predictable, and robust. Widely used in industrial PI tuning for first-order
processes.

**On frequency domain vs. time domain design:** Bode margins give robustness
guarantees that root locus alone cannot — a system can have acceptable transient
response but dangerously low phase margin. The Day 5B satellite antenna case
showed GM = 38.83 dB and PM = 59.02° at K = 30, meaning the system tolerates
a 2623× gain increase and 59° of phase lag before going unstable. That margin
quantifies how much model uncertainty the design can absorb — the number
every control engineer is asked for in a design review.
**On controller architecture selection (I-P vs PI):** The Day 6 project
demonstrated that meeting a zero-overshoot requirement sometimes means
deliberately choosing a slower architecture. A PI controller with ζ = 1
still produced 11.36% overshoot due to the numerator zero — the only way
to eliminate it was restructuring the loop topology. This is a real design
decision that appears in every Oil & Gas level control specification, where
the consequence of overshoot is an environmental or quality violation, not
just a performance metric.

**On infinite gain margin:** A first-order plant controlled by a pure
integrator produces GM = ∞. This is not a calculation artifact — it means
the closed-loop characteristic equation has no solution at −180° phase,
so Nyquist stability is guaranteed regardless of gain. Recognizing this
class of system on sight saves significant analysis time during field
commissioning.
## How to Run

**Requirements:** MATLAB R2022b or later · Control System Toolbox

1. Open `damped_systems.slx` in Simulink and run the model.
2. Open `Portafolio_week_1.mlx` in MATLAB and run each section sequentially.
3. For the Day 6 separator level control project, open `Interface_level_project.mlx`,
   then run `Interface_level.slx` to simulate the closed-loop controller performance.

## About

Marcos Fierro · Mechatronics Engineering · Universidad Nacional de Colombia  
Internship: SLB (Schlumberger) — Well Construction, Product Engineering · Jun–Dec 2025  
Developed DASCF/InfoBits: automated drilling performance analytics tools (Python + Power BI) getting the 97% reduction in manual reporting time.

[LinkedIn](https://linkedin.com/in/marcos-alfredo-fierro-sarria) · [GitHub](https://github.com/MarcosAlferoFierroSarria)







