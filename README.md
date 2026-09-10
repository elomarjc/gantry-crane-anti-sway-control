# ⚙️ Real-Time Anti-Sway Control of a 2D Gantry Crane

<div align="center">

# Anti-Sway Gantry Crane Control System
### Bachelor's Project • Electronic Systems & Control Engineering
**Aalborg University (AAU) • Group 612**

[![Platform](https://img.shields.io/badge/PlatformIO-Arduino_Mega_2560-orange?style=for-the-badge&logo=platformio&logoColor=white)](https://platformio.org)
[![Language](https://img.shields.io/badge/Language-C_%2F_C%2B%2B-blue?style=for-the-badge&logo=c%2B%2B)](https://github.com/elomarjc)
[![Domain](https://img.shields.io/badge/Domain-Embedded_Control_Engineering-red?style=for-the-badge)](https://github.com/elomarjc)
[![Academic](https://img.shields.io/badge/AAU-Electronic_Systems-0C2340?style=for-the-badge)](https://www.aau.dk)

</div>

---

## 🏗️ Project Overview & Control Challenge

Industrial gantry cranes transport suspended payloads across manufacturing yards, container terminals, and construction sites. A critical operational challenge is that the suspended payload behaves as an **underactuated pendulum**: rapid acceleration or deceleration of the trolley induces dangerous load sway ($\theta$).

This project delivers the dynamic modeling, embedded firmware, sensor calibration, and discrete-time feedback control system for a physical laboratory-scale 2D gantry crane. The objective is to achieve rapid, accurate point-to-point trolley positioning while actively damping payload oscillations throughout the transit trajectory.

---

## 🧠 Control Architecture

The system utilizes a **cascaded multi-loop discrete PID control topology** operating on an Atmel ATmega2560 microcontroller:

```
                          ┌────────────────────────────────────────────────────────┐
                          │                   OUTER LOOP (TROLLEY)                 │
                          │                                                        │
    Target X Ref ───────►(+)──►[ X Position PID ]──►(+)─────────────────►[ Motor ]──► Trolley (X)
                          ▲ -                        ▲ +                    │
                          │                          │                      ▼
                          └────── Potentiometer ─────┼────────────► [ Low-Pass Filter ]
                                                     │                      │
                                                     │                      ▼
                                          [ Sway Angle PID ]◄──(-)─ θ Angle Sensor
                                                     │
                                                     └─────────────────────────────┘
                                                          INNER LOOP (ANTI-SWAY)
```

### 1. Dual-Loop Control Strategy
* **Inner Loop (Active Damping)**: Regulates the pendulum deflection angle $\theta$ back to equilibrium ($\theta = 0$). Dynamic angle feedback modifies the effective acceleration command to counteract inertial sway forces.
* **Outer Loop (Position Tracking)**: Regulates the physical displacement of the trolley ($x$) and vertical hoist ($y$) to target reference coordinates.
* **Actuator Saturation & Anti-Windup**: Implements bounded output clamping to protect DC motor drivers from overcurrent states while preventing integrator windup.

### 2. Embedded Digital Signal Processing (`sigProc`)
* **Low-Pass Filter**: Raw analog sensor inputs from positioning potentiometers are filtered through a discrete first-order low-pass filter ($\tau = 30\text{ ms}$) to reject electrical switching noise from high-current motor PWM lines.
* **Velocity Estimation**: Implements Forward Euler discrete differentiation on filtered position samples to compute real-time trolley velocity without requiring costly tachometer hardware.

---

## 🔌 Hardware Testbed Specifications

| Component | Hardware / Protocol | Role in System |
| :--- | :--- | :--- |
| **Microcontroller** | **ATmega2560 (Arduino Mega)** | Real-time control loop execution, timer interrupts, ADC conversions. |
| **Actuators** | **Dual High-Torque DC Motors** | Independent drive motors for horizontal trolley ($x$) and vertical hoist ($y$). |
| **Motor Drivers** | **PWM H-Bridge Inverters** | Bidirectional variable-voltage speed and torque actuation. |
| **Position Sensors** | **Precision Rotary Potentiometers** | Linear analog voltage feedback proportional to trolley displacement. |
| **Sway Sensor** | **Optical Encoder / Angle Sensor** | High-resolution pendulum deflection detection at the suspension pivot. |
| **Custom Housing** | **Custom 3D-Printed Enclosure** | Modular mounting chassis engineered for the Arduino Mega and sensor harnesses. |

---

## 📊 Experimental Validation & Acceptance Tests

The physical system underwent comprehensive experimental qualification:
1. **Sensor Linearity Tests**: Validated linear ADC-to-millimeter and ADC-to-degree calibration across the entire physical crane span.
2. **Step Response Analysis**: Conducted empirical step tests comparing theoretical closed-loop mathematical models against physical crane step responses ($X$, $Y$, and $\theta$).
3. **Point-to-Point Transit Verification**: Tested repeatable transit trajectories ($A \to B \to A$) demonstrating rapid settling times with zero residual steady-state payload oscillations upon arrival.

---

## 📁 Repository Structure

```
P6-Crane-612/
├── Main/
│   ├── src/
│   │   ├── Main.cpp              # Core real-time control loop & state machine
│   │   ├── new_controller.cpp    # Experimental cascaded control implementations
│   │   ├── ANGLEsteptest.cpp     # Dedicated pendulum angle step validation routine
│   │   └── Xsteptest.cpp         # Trolley horizontal position step test routine
│   ├── lib/
│   │   ├── PID_v1/              # Industrial discrete PID implementation with anti-windup
│   │   └── sigProc/             # Signal processing routines (low-pass, Euler derivative)
│   ├── data/                     # Experimental step test CSV/MAT files & acceptance test reports
│   └── platformio.ini            # PlatformIO compilation environment definition
└── Simple_gantry_code/           # Baseline standalone Arduino sketch for initial hardware checkout
```

---

## 🚀 Build & Compilation (PlatformIO)

```bash
# Clone the repository
git clone https://github.com/elomarjc/gantry-crane-anti-sway-control.git
cd gantry-crane-anti-sway-control/Main

# Compile and upload firmware via PlatformIO CLI
pio run --target upload
```

---

## 🎓 Academic Context

* **Course**: 6th Semester Bachelor Project in Electronic Systems
* **Institution**: Aalborg University, Denmark (AAU)
* **Semester / Year**: Spring 2023 • Group 612
* **Discipline**: Embedded Systems, Control Theory, and Real-Time Signal Processing
