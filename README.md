# Analog PID Controller  

## Overview  
This project is a **fully analog PID (Proportional–Integral–Derivative) controller** designed, implemented, and tested entirely in hardware. The system takes an input error signal and produces a corrected control output in real time using only analog circuitry - no software, no microcontrollers, just pure circuit behavior.

The purpose of this project is to bridge control theory with real-world electronics by physically realizing how systems respond to error, time, and change through carefully designed circuits.

---

## What It Does  
The controller continuously monitors an input signal representing system error and generates a stabilized output that can be used to regulate real-world systems such as motors, temperature systems, or voltage levels.

It achieves this through three core components:

- **Proportional (P):** Provides an immediate response proportional to the current error  
- **Integral (I):** Accumulates past error to eliminate steady-state offset  
- **Derivative (D):** Responds to the rate of change of error to reduce overshoot and improve stability  

These three signals are combined in real time to produce a smooth, stable control output without any digital processing.

---

## Why This Project Matters  
Modern control systems are typically implemented in software, which abstracts away the underlying physics. This project removes that abstraction and demonstrates control systems at a fundamental level.

Here, system behavior is not programmed—it is **physically engineered**:
- Gain is controlled by resistor ratios  
- Time response is shaped by capacitors  
- Stability is influenced by circuit design and layout  
- Noise and imperfections must be managed directly  

This makes the project a true representation of how control systems behave in the real world.

---

## System Design  

### Schematic Design  
- Designed in KiCad from the ground up  
- Separate analog blocks for P, I, and D stages  
- Operational amplifiers used for signal processing  
- Component values selected to match desired gains and time constants  

### PCB Design  
- Custom PCB layout created in KiCad  
- Clean routing to preserve signal integrity  
- Grounding strategies implemented to reduce noise  
- Designed with real-world constraints in mind  

### Hardware Implementation  
- PCB fabricated and assembled  
- Components soldered and verified  
- System tested under real electrical conditions  

---

## Testing & Validation  
The controller is tested using standard lab equipment to evaluate real-world performance:

- **Oscilloscope:** Observe transient and steady-state response  
- **Function Generator:** Provide controlled input/error signals  
- **Power Supply:** Drive the system  

Performance is evaluated based on:
- Rise time  
- Overshoot  
- Settling time  
- Stability  
- Steady-state error  

All results are observed directly from hardware behavior, not simulation alone.

---

---

## Tools & Technologies  
- **KiCad** – schematic capture and PCB design  
- **Operational Amplifiers** – core analog computation  
- **Passive Components** – resistors and capacitors for gain and timing  
- **Oscilloscope** – signal analysis  
- **Function Generator** – input signal generation  

---

## Future Improvements  
- Add potentiometers for real-time tuning of Kp, Ki, and Kd  
- Integrate with a physical system (motor, thermal system, etc.)  
- Improve derivative stage noise filtering  
- Compare performance against a digital PID controller  
- Optimize PCB layout for higher-frequency stability  

---

## Contributors  
- Devansh Joshi  
- Suren Shirani  

---

## Final Note  
This project focuses on understanding control systems at their most fundamental level—through real signals, real components, and real-world behavior. It demonstrates how abstract mathematical models translate directly into physical systems, where every design choice has a measurable impact.
