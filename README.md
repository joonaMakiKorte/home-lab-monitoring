# Home Lab Monitoring

## Overview

A complete **Infrastructure-as-Code (IaC)** monitoring stack that spins up in seconds using Docker Compose. It captures high-resolution system metrics, retains historical data with optimized retention policies, and visualizes it all in a Grafana dashboard. 

This project acts as en extention to my [Smart Home Dashboard](https://github.com/joonaMakiKorte/smarthome) project, specifically designed for monitoring server hardware and infrastructure health.

## Raspberry Pi Monitoring
The Raspberry Pi node monitors **System Vital Signs**, **Docker Health**, and **Network & Services**. **Pi-hole** and **Network** stats are integrated via Telegraf's HTTP input.

* **Collector: Telegraf** runs as the central agen on the Raspberry Pi. It aggregates system metrics, queries Docker APIs, and pulls application stats.
* **Storage: InfluxDB** receives these metrics and stores them efficiently (using retention policies optimized for SD card longetivity).

### Monitoring Dashboard
**System Stats**
<img width="1679" height="659" alt="pi-system" src="https://github.com/user-attachments/assets/edb2d02f-c777-4e95-a3ca-bf47b6bc111c" />
**Docker Health**
<img width="1667" height="260" alt="pi-docker" src="https://github.com/user-attachments/assets/cbee278a-caf8-4e96-9541-ede672e2b332" />
**Network Health**
<img width="1673" height="486" alt="pi-network" src="https://github.com/user-attachments/assets/bd951368-0786-4ff5-856f-c9691985a385" />

## Fedora Server Monitoring
The heavy lifting of the monitoring stack runs on a separate Fedora machine (laptop/server). Acting as the centralized **Observability Hub**, it aggregates data from remote nodes (like the Raspberry Pi) while monitoring its own performance using cloud-native standards.

* **Visualization Host:** Runs the central **Grafana** instance that visualizes data from all sources (InfluxDB on the Pi, local Prometheus).
* **Infrastructure as Code (IaC):** Utilizes **Grafana Provisioning** to automatically load dashboards and datasources from configuration files on startup. This ensures the monitoring environment is stateless, version-controlled, and instantly reproducible without manual setup.
* **Self-Monitoring:** Uses a **Prometheus + Node Exporter** stack to scrape high-resolution system metrics (CPU, Memory, Disk I/O) from the Fedora host itself, ensuring the monitoring server is healthy and performant.

### Monitoring Dashboard
For the Fedora server monitoring, we used the community standard "Node Exporter Full" dashboard.
* **Dashboard Details:**
  * **Name:** Node Exporter Full
  * **ID:** `1860`
  * **Link:** [Node Exporter Full on Grafana.com](https://grafana.com/grafana/dashboards/1860-node-exporter-full/)
- **Snapshot from the official website:**
<img width="1918" height="947" alt="image" src="https://github.com/user-attachments/assets/1ca16cba-fa4a-4f9b-b4da-8135b557ce6b" />

## Architecture
```plaintext
home-lab-monitoring/
├── raspberry-pi/                # The "Edge" Collector
│   ├── docker-compose.yml       # Runs Telegraf & InfluxDB
│   ├── .env                     # Secrets (InfluxDB Passwords)
│   └── telegraf/
│       └── telegraf.conf        # Config for inputs (Docker, System, HTTP connections)
│
├── fedora-server/               # The "Core" Visualization Hub
│   ├── docker-compose.yml       # Runs Grafana & Prometheus
│   ├── prometheus/
│   │   └── prometheus.yml       # Scrapes local Fedora metrics
│   └── grafana/
│       ├── dashboards/          # The JSON "Code" for your dashboards
│       │   ├── raspberry-pi.json
│       │   └── node-exporter-full.json
│       └── provisioning/        # Auto-loaders
│           └── dashboards/
│               └── dashboard.yml # Tells Grafana where to find the JSON files
│
└── README.md                    # Documentation
```

## Getting Started
Follow these steps to deploy the monitoring stack.

### Prerequisites
- **Docker** and **Docker Compose** installed on both the Raspberry Pi and the Fedora Server.
- **Git** installed on the repository.

**1. Clone the Repository**

**Option A: Fedora Server (Standard Clone)**
Run this on your Fedora machine to download the full repository:
```bash
git clone https://github.com/joonaMakiKorte/home-lab-monitoring.git
cd home-lab-monitoring
```

**Option B: Raspberry Pi (Sparse Clone)**
Run this on your Raspberry Pi to download **only** the necessary Pi files (cleaner and saves space):
```bash
# 1. Clone in sparse mode (downloads history but no files initially)
git clone --no-checkout https://github.com/joonaMakiKorte/home-lab-monitoring.git
cd home-lab-monitoring

# 2. Initialize sparse checkout
git sparse-checkout init --cone

# 3. Download only the 'raspberry-pi' folder
git sparse-checkout set raspberry-pi
git checkout main
```

**2. Setup: Raspberry Pi (Edge Collector)**
*This node collects data and stores it locally in InfluxDB.*
  1. Navigate to the Pi folder:
     ```bash
     cd raspberry-pi
     ```
  2. Create the `.env`file:
     ```bash
     nano .env
     ```
  3. Paste the following template with your own values:
     ```Ini
     INFLUX_USER=admin
     INFLUX_PASSWORD=...
     INFLUX_ORG=...
     INFLUX_BUCKET=...
     INFLUX_TOKEN=...

     DOCKER_GID=...      # Your Docker Group ID
     RASP_IP=...         # IP addr of your Raspberry Pi
     ```
  4. Start the stack:
     ```bash
     docker compose up -d
     ```

**3. Setup: Fedora Server (Control Hub)**
*This node hosts the Dashboard and monitors itself.*
  1. Navigate to the Server folder:
     ```bash
     cd fedora-server
     ```
  2. Create the `.env` file:
     ```bash
     nano .env
     ```
  3. Start the stack:
     ```bash
     docker compose up -d
     ```

**4. Access the Dashboard**
  1. Open your web browser and navigate to the IP address of your **Fedora Server**:
  2. **First Login:**
     - **Username:** `admin`
     - **Password:** `admin`
  3. **Secure Your Instance:**
     - Grafana will immediately ask you to set a **new, secure password**. Enter your chosen password now.
The dashboards "Raspberry Pi Monitor" and "Node Exporter Full" will be pro-loaded and waiting for you in the **Dashboards** section.

## Licence
Distributed under the MIT License. See `LICENSE` for more information.




