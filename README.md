# Prometheus & Grafana Monitoring Lab

A complete monitoring solution using Prometheus and Grafana deployed on AWS EC2 with Docker Compose.

## Architecture Overview

This project sets up a monitoring stack consisting of:
- **Prometheus**: Metrics collection and storage
- **Node Exporter**: System metrics exporter
- **Grafana**: Visualization and dashboards

## Prerequisites

- AWS EC2 instance (Ubuntu/Amazon Linux)
- Docker and Docker Compose installed
- Security groups configured for ports 3000, 9090, 9100

## Quick Setup

### 1. EC2 Instance Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. Security Group Configuration

Open these ports in your EC2 security group:
- **3000**: Grafana web interface
- **9090**: Prometheus web interface
- **9100**: Node Exporter metrics
- **22**: SSH access

### 3. Deploy the Stack

```bash
# Clone/upload project files
# Navigate to project directory
cd Guided-Monitoring-Lab

# Start all services
docker-compose up -d

# Verify containers are running
docker-compose ps
```

## Access URLs

- **Grafana**: `http://<EC2-PUBLIC-IP>:3000`
  - Default login: admin/admin
- **Prometheus**: `http://<EC2-PUBLIC-IP>:9090`
- **Node Exporter**: `http://<EC2-PUBLIC-IP>:9100/metrics`

## Configuration

### Grafana Data Source Setup

1. Login to Grafana
2. Go to Configuration → Data Sources
3. Add Prometheus data source:
   - URL: `http://prometheus:9090`
   - Access: Server (default)
4. Save & Test

### Creating Dashboards

1. Import Node Exporter dashboard (ID: 1860)
2. Or create custom dashboards using metrics like:
   - `node_cpu_seconds_total`
   - `node_memory_MemAvailable_bytes`
   - `node_filesystem_size_bytes`

## File Structure

```
Guided-Monitoring-Lab/
├── compose.yml          # Docker Compose configuration
├── prometheus.yml       # Prometheus scrape configuration
├── README.md           # This file
└── Screenshots/        # Dashboard screenshots
```

## Monitoring Metrics

The setup monitors:
- CPU usage and load
- Memory utilization
- Disk space and I/O
- Network statistics
- System uptime

## Troubleshooting

```bash
# Check container logs
docker-compose logs prometheus
docker-compose logs grafana
docker-compose logs node-exporter

# Restart services
docker-compose restart

# Stop all services
docker-compose down
```

## Slack & Email Alerts Setup

### 1. Slack Integration
1. Create Slack webhook: https://api.slack.com/messaging/webhooks
2. Update `alertmanager.yml` with your webhook URL
3. Set channel name (e.g., #alerts)

### 2. Email Integration
1. Use Gmail app password or SMTP server
2. Update `alertmanager.yml` with SMTP details and credentials

### 3. Deploy with Alerts
```bash
docker-compose down
docker-compose up -d
```

### 4. Access Alertmanager
- URL: `http://<EC2-IP>:9093`
- Port 9093 must be open in security group

## Cleanup

```bash
# Remove containers and networks
docker-compose down

# Remove volumes (data will be lost)
docker-compose down -v
```
