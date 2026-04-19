# NYCU AUV — STM32 Firmware

This repository contains the STM32-based low-level layer for the NYCU Autonomous Underwater Vehicle (AUV), developed for competition in the 2022 Singapore Autonomous Underwater Vehicle Challenge (SAUVC). The team qualified among 93 teams from 19 countries and competed on-site in Singapore.

> **Note:** This repository covers the low-level embedded layer running on the STM32F407. The high-level layer (ROS, task planning, computer vision) is not included as those files are no longer available, but the full system architecture is documented below for reference.

---

## Achievements

- 🥈 **Silver Medal Prize** — Intelligent Robots Division, 2021 Intelligent Innovation and Interdisciplinary Creation Contest (327 teams)
- 🏆 **Cross-domain Integrated Special Award** — 2021 Intelligent Innovation and Interdisciplinary Creation Contest
- 🌏 **Qualified and competed** — 2022 Singapore Autonomous Underwater Vehicle Challenge (SAUVC), competing against 93 teams from 19 countries

---

## System Architecture

The AUV runs a two-layer software architecture separating high-level autonomy from low-level real-time control.

![System Architecture](assets/auv_architecture.png)

### High-level layer

| Component | Responsibilities |
|-----------|-----------------|
| Raspberry Pi | Task planning, navigation, sonar-based localization |
| Jetson Nano | Computer vision, object detection |

The Raspberry Pi and Jetson Nano communicate over **ROS**. High-level commands are sent to the STM32 over **UART**.

### Low-level layer (this repo)

The **STM32F407** handles all real-time sensing and control at high frequency, including:

- **Attitude estimation** — Gradient descent-based Madgwick filter on MPU-9250 IMU data, fused with sonar yaw for drift correction. Achieved a 10% reduction in high-frequency noise compared to a naive complementary filter.
- **Attitude & velocity control** — Quaternion-based geometric controller computing thrust allocation across 6 T200 thrusters for 6-DOF motion.
- **Depth control** — Pressure sensor (Bar02) integration for closed-loop depth regulation.
- **DVL integration** — Doppler Velocity Log reader for underwater velocity estimation.

### Sensors

| Sensor | Interface | Consumer |
|--------|-----------|----------|
| MPU-9250 IMU | SPI | STM32 |
| Bar02 pressure sensor | I2C | STM32 |
| DVL | UART | STM32 |
| Sonar | — | Raspberry Pi |
| Stereo camera | — | Jetson Nano |

### Actuators

| Actuator | Count | Driver |
|----------|-------|--------|
| BlueRobotics T200 thrusters | 6 | STM32 PWM (TIM) |

---

## Repository Structure

```
NYCU_AUV_STM32_Control/
├── Core/
│   ├── Inc/
│   │   ├── Datatype/       # Vector and quaternion types
│   │   ├── Propulsion_Sys/ # Thruster allocation and T200 motor interface
│   │   ├── Sensor/         # IMU (MPU-9250), pressure (Bar02), Madgwick filter
│   │   ├── controller.h    # Geometric attitude/velocity controller
│   │   ├── dvl_reader.h    # DVL UART parser
│   │   └── read_data.h     # UART communication with high-level layer
│   └── Src/                # Implementation files
└── Drivers/                # STM32 HAL libraries
```

---

## Requirements

- STM32CubeIDE or compatible toolchain
- Target board: STM32F407
- STM32 HAL libraries (included in `Drivers/`)

---

> This repository is maintained for archival and portfolio purposes.