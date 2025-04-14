# Simple monitoring Stack: Prometheus and Grafana

## Overview

This repository contains a minimal monitoring solution using Prometheus, Grafana, and Node Exporter. The stack allows you to collect system metrics from your host and visualize them through a customizable Grafana dashboard.

## Components

- **Prometheus**: Time-series database for storing metrics
- **Grafana**: Visualization and dashboarding tool
- **Node Exporter**: Agent that collects and exposes host system metrics

## Prerequisites

- Docker & Docker Compose
- Bash shell (for user management script)
- `jq` utility (for parsing JSON in the user management script)

## Getting Started

### 1. Configure Prometheus

Edit the `prometheus.yml` file and replace `your_ip` with your actual server IP address:

```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['your_ip:9100']
```

### 2. Configure User Management Script

Edit the `create_grafana_user.sh` script and replace `your_server_ip` with your actual server IP address:

```bash
GRAFANA_URL="http://your_server_ip:3000"
```

### 3. Start the Stack

```bash
docker-compose up -d
```

This will start Prometheus, Grafana, and Node Exporter containers.

## Access the Services

- **Prometheus**: http://your-vps-ip>:9090
- **Grafana**: http://your-vps-ip>:3000 (default credentials: admin/admin)
- **Node Exporter**: Metrics available at http://your-vps-ip>:9100/metrics

### 4. Configure Grafana

Open your browser and navigate to http://<your-vps-ip>:3000.

Log in with the default admin credentials

Add Prometheus as a Data Source. Go to Configuration > Data Sources and click Add data source, choose Prometheus.

Set the URL to http://your_ip:9090 

Click Save & Test.

You can import community dashboards (e.g., search for "Node Exporter Full" on Grafana Dashboards) to visualize CPU and other system metrics.

## User Management

### Creating New Grafana Users

The included bash script allows you to create new users with viewer permissions:

```bash
chmod +x create_grafana_user.sh
./create_grafana_user.sh
```

Follow the prompts to:
1. Enter admin credentials
2. Provide new user details (name, email, login, password)

The script will:
- Create a new user via Grafana API
- Assign the "Viewer" role to the new user

## Configuration Details

### Docker Compose

The `docker-compose.yml` file defines the following services:

- **Prometheus**:
  - Image: prom/prometheus:v3.2.0
  - Port: 9090
  - Mounts local prometheus.yml configuration

- **Grafana**:
  - Image: grafana/grafana:11.5.2
  - Port: 3000
  - Persistent volume for data storage

- **Node Exporter**:
  - Image: prom/node-exporter:v1.9.0
  - Uses host networking to access host metrics
  - Mounts host system directories to collect data

### Volumes

- `grafana-storage`: Persistent volume for Grafana data

## Important Notes

- Node Exporter uses host networking which only works on Linux hosts
- Default Grafana credentials should be changed after first login
- The user management script requires direct network access to the Grafana service

## Customization

### Adding Additional Exporters

To monitor additional services, add more exporters to the `docker-compose.yml` file and update the Prometheus configuration to scrape them.

### Grafana Dashboards

After logging in to Grafana:
1. Go to Dashboards > New > Import
2. Import popular Node Exporter dashboards using ID `1860` or `11074`

## Troubleshooting

- If metrics aren't showing up, check that your IP addresses are correctly configured
- Ensure Node Exporter is accessible from Prometheus
- Check container logs with `docker-compose logs <service_name>`
