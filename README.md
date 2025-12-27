# Home Lab Monitoring

## Overview

A complete Infrastructure-as-Code (IaC) monitoring stack that spins up in seconds using Docker Compose. Captures high-resolution system metrics, retains historical data with optimized retention policies, and visualizes it all in a Grafana dashboard. Acts as an extention to my [Smart Home Dashboard](https://github.com/joonaMakiKorte/smarthome) -project for monitoring the server hardware.

## Raspberry Pi Monitoring

- **Collector: Telegraf** runs as the central agen on the Raspberry Pi. It aggregates system metrics, queries Docker APIs, and pulls application stats.
- **Storage: InfluxDB** receives these metrics and stores them efficiently (using retention policies optimized for SD card longetivity).

### Monitoring Dashboard
- **System**
  <img width="1679" height="659" alt="pi-system" src="https://github.com/user-attachments/assets/edb2d02f-c777-4e95-a3ca-bf47b6bc111c" />
- **Docker**
  <img width="1667" height="260" alt="pi-docker" src="https://github.com/user-attachments/assets/cbee278a-caf8-4e96-9541-ede672e2b332" />
- **Network**
  <img width="1673" height="486" alt="pi-network" src="https://github.com/user-attachments/assets/bd951368-0786-4ff5-856f-c9691985a385" />

