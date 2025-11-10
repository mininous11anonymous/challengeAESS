# EPS Monitoring Prototype

The **EPS (Electrical Power System) monitoring prototype** is built entirely from low-power, space-compatible components. This system is designed for CubeSat applications, providing real-time data collection, efficient energy management, and safe electronic control under stringent power constraints.

## Key Components

The table below summarizes the main electronic elements of the system and their respective roles:

| Component             | Key Characteristics                     | Function / Objective                                                   |
|-----------------------|----------------------------------------|------------------------------------------------------------------------|
| **ESP32-WROOM-32**    | Dual-core 240 MHz, 520 KB RAM, ESP-IDF SDK | Main controller — Core 0 handles sensor data, Core 1 runs AI tasks    |
| **INA219**            | I2C current/voltage sensor, ±3.2 A range | Monitors battery, solar panels, and 3.3 V / 5 V power rails           |
| **LM35**              | Analog sensor, 10 mV/°C, 0–100 °C range | Measures battery and MCU temperatures                                   |
| **ADP5090**           | Energy-harvesting PMIC, buck-boost converter | Manages and optimizes energy from solar panels                        |
| **Logic-Level MOSFET**| Low RDS(on), 3.3 V gate drive           | Enables safe battery isolation and load switching during faults       |

*Table 2.1 – Key electronic components of the EPS monitoring prototype*

## System Overview

This prototype integrates all components to ensure:  

- **Real-time monitoring:** Continuous tracking of voltage, current, and temperature across the system.  
- **Efficient energy management:** Optimized harvesting and distribution of energy from solar panels.  
- **Safe operation:** Controlled load switching and battery isolation to prevent faults.  

## Usage

1. Power the EPS prototype using a suitable solar panel or battery source.  
2. Connect sensors (INA219, LM35) to their respective measurement points.  
3. Upload the firmware to the ESP32 using **ESP-IDF**.  
4. Monitor sensor readings via serial output or through integrated AI-based anomaly detection modules.  

