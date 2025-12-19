## Video


<div align="center">

[![Video](https://img.youtube.com/vi/zsB7oTcueuI/maxresdefault.jpg)](https://www.youtube.com/watch?v=zsB7oTcueuI)

**Click the image above to play the video in a new window.**

</div>

# 2DOF Thrust-Vectoring Bicopter (2DOF-TVB)
## Large-Pitch Maneuvers Enabled by Quaternion Finite-Time Control (Blog-style Overview)

This repo is a **blog-style technical introduction** to a 2DOF thrust-vectoring bicopter that can stably operate at **large pitch angles (up to ±90°)** and perform contact-oriented behaviors such as **vertical wall adhesion**.  
This README explains the **idea**, **mechanism**, **modeling choices**, and **control stack** at a system level.

---

## Why this matters: from “flying camera” to “flying tool”
Aerial robots are increasingly expected to do more than observe. Tasks like vertical inspection, surface interaction, and inclined landing require **aggressive attitude changes** while maintaining stable motion.  
Conventional under-actuated multirotors—and even typical **1-DoF thrust-vectoring bicopters**—tend to suffer from **pitch–longitudinal coupling** in large-pitch maneuvers, which shrinks the feasible operating envelope when you need to “lean in” hard.

---

## The key hardware idea: build decoupling into the mechanism
The platform uses a **two-stage, dual-servo series mechanism** on each arm, giving the thrust vector **two rotational degrees of freedom** (two parallel rotation axes).

- Stage-1 servo angle: **αᵢ ∈ [−45°, 45°]**
- Stage-2 servo angle: **βᵢ ∈ [−135°, 135°]**

The practical consequence: the design aims to **decouple body pitch from longitudinal acceleration / thrust direction**, so the vehicle can pitch aggressively without “dragging” the translational dynamics along for the ride.

> Intuition: when the *mechanism* removes intrinsic coupling, the controller doesn’t have to fight geometry at extreme attitudes.

---

## Modeling choice: quaternions for singularity-free large-attitude flight
Large pitch angles near ±90° are exactly where Euler angles become annoying (or outright singular).  
This work therefore models attitude with **quaternions**, and aggregates uncertainties such as external disturbances and **center-of-gravity (CoG) shifts** into **lumped disturbance terms**—a control-friendly way to represent what you can’t perfectly know.

---

## Control architecture: fast, robust outer loops + feasible inner allocation
You can think of the control stack as two layers:

### 1) Outer loop: finite-time tracking with disturbance estimation
The outer loop uses a **non-singular fast terminal sliding-mode controller (NFTSMC)** to obtain **finite-time convergence** for tracking, paired with a **higher-order sliding-mode observer (HOSMO)** to estimate disturbances and reduce chattering.

### 2) Inner loop: Dynamic Control Allocation (DCA) for an over-actuated system
A 2DOF-TVB has actuator redundancy: you need to realize desired forces/moments while commanding **motor thrusts + servo angles** under hard constraints (saturation, limits, etc.).  
Instead of static allocation (e.g., pseudo-inverse), the system uses **Dynamic Control Allocation (DCA)** formulated as **real-time constrained optimization** (solved via real-time gradient optimization), explicitly addressing feasibility under constraints and CoG uncertainty.

> In over-actuated aerial robots, allocation isn’t a detail—it’s often where reliability is won or lost.

---

## Evidence: large-pitch hovering and wall adhesion
The work validates the concept with both simulation and real flight experiments, including:

- **Large-pitch hover** at commanded pitch angles **0°, 30°, 60°, and 90°**, with tracking metrics reported using quaternion errors.
- A **vertical wall-adhesion** sequence, transitioning from near-level flight to ~90° pitch and sustaining controlled interaction.

A prototype implementation is described with typical research-grade avionics (e.g., Pixhawk-class controller), RTK positioning, high-torque servos, and a lightweight airframe.

---

## What to take away (engineering lessons)
- **Mechanical decoupling is a force multiplier**: reduce coupling at the source to expand the feasible envelope.
- **Quaternions are the right language** for extreme attitudes and continuous maneuvers.
- **Dynamic allocation is a core capability** in redundant actuation: feasibility, saturation, and uncertainty must be handled explicitly.

---

## Citation
If you reference this project write-up, please cite the original paper included in this repository.



