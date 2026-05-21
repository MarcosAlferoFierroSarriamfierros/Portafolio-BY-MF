# Mechatronic Engineering — Industrial Portfolio
## Week 1: Feedback Control & PID Design | Mechatronics Engineering · Universidad Nacional de Colombia

## Overview
This repository documents hands-on control systems design exercises developed during Week 1 (18/05/2026) of a structured industrial engineering portfolio (for now)

All problems are grounded in real-world applications like oil & gas drilling operations, motoring, and robotic pick-and-place manufacturing. These exercise had solved using MATLAB and Simulink with full analysis and their respective simulations.
This work follows Norman Nises's Control Systems Engineering as the primary reference. Some of the excercises had formulated using AI, extending each exercise beyond the textbook with industrial context and quantified performance validation.

Target roles:
Control and Automation Engineer, Robotics Systems Engineer, Mechatronics Systems Engineer, Field Engineer (Oil & Gas), Manufacturing Engineer, Toy Design Engineer, Automotive Engineer, and related roles.

## Engineering Results at a Glance for Week 1
ExerciseSystemMethodKey ResultDay 1Block diagram algebrafeedback(), series(), pzmap()Closed-loop TF verified analytically and numericallyDay 2Pitch angle dynamics (aircraft)2nd-order approximation validityApproximation invalid — zero at −0.435 only 3.84× from dominant poles (threshold: 5×)Day 2Active suspension — F1 contextstepinfo(), damping analysisζ = 0.517, ωₙ = 1.93 rad/s → OS = 15%, tₛ = 4.06 s ✓Day 3Root locus — RHP zerosrlocus(), Routh-HurwitzK_critical = 1.5 confirmed analytically and graphicallyDay 3P controller — ABB IRB 1200Root locus designK = 13.2 → OS reduced from 57% to 19.4%, tₛ from 7.31 s to 2.7 sDay 4PD+I controller designAngle/magnitude conditionCompensator zero at −65.99, K = 121.5 → meets %OS = 20%, Tp = 0.198 s

## Project Structure
Currently, the repository has three folders: one for results ("results"), one for Matlab scripts ("Matlab"), and one for Simulink files ("Simulink"). The structure of this repository will likely change as progress is made.

The executive summary of the work done on the scripts, as well as the results obtained, can be found in this Readme file.

## Activity by days
### Day 1: Transfer Functions & Block Diagram Algebra
A system was assembled using block diagrams, and an AI-provided exercise was developed in the Oil & Gas context.


## About
Marcos Fierro · Mechatronics Engineering · Universidad Nacional de Colombia
Internship: SLB (Schlumberger) — Well Construction, Product Engineering · Jun–Dec 2025
Developed DASCF/InfoBits: automated drilling performance analytics tools (Python + Power BI) — 97% reduction in manual reporting time. Final note: 5.0/5.0
