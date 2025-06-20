# Alerting System Documentation

## Architecture
The alerting system includes:
- **Prometheus**: Evaluates alert rules and sends to Alertmanager
- **Alertmanager**: Routes notifications to Slack/Email
- **Alert Rules**: Define conditions for CPU, Memory, Disk alerts

## Alert Rules Configuration

| Alert | Condition | Duration | Severity |
|-------|-----------|----------|----------|
| HighCPUUsage | CPU > 80% | 2 minutes | warning |
| HighMemoryUsage | Memory > 85% | 2 minutes | warning |
| DiskSpaceLow | Disk > 90% | 1 minute | critical |

## Slack Integration Setup

1. **Create Slack App:**
   - Go to https://api.slack.com/apps
   - Click "Create New App" → "From scratch"
   - Enable "Incoming Webhooks"
   - Add webhook to workspace and select channel
   - Copy webhook URL

2. **Update alertmanager.yml:**
   ```yaml
   slack_configs:
     - api_url: "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"
       channel: "#your-channel"
   ```

## Email Integration Setup

1. **Gmail App Password:**
   - Enable 2FA on Gmail account
   - Go to Google Account → Security → App passwords
   - Generate app password for "Mail"

2. **Update alertmanager.yml:**
   ```yaml
   global:
     smtp_smarthost: "smtp.gmail.com:587"
     smtp_from: "your-email@gmail.com"
     smtp_auth_username: "your-email@gmail.com"
     smtp_auth_password: "your-app-password"
   ```

## Files Added for Alerting
- `alertmanager.yml` - Notification routing configuration
- `alert-rules.yml` - Alert conditions and thresholds

## Deploy with Alerts
```bash
# Add port 9093 to security group first
docker-compose down
docker-compose up -d

# Verify alertmanager is running
docker-compose logs alertmanager
```

## Access Points
- **Alertmanager UI**: `http://<EC2-IP>:9093`
- **Prometheus Alerts**: `http://<EC2-IP>:9090/alerts`
- **Prometheus Rules**: `http://<EC2-IP>:9090/rules`

## Testing Alerts

### Method 1: CPU Stress Test
```bash
# Install stress tool
sudo apt install stress -y

# Stress CPU for 5 minutes (triggers HighCPUUsage)
stress --cpu 4 --timeout 300
```

### Method 2: Memory Stress Test
```bash
# Stress memory (triggers HighMemoryUsage)
stress --vm 2 --vm-bytes 1G --timeout 300
```

### Method 3: Disk Space Test
```bash
# Create large file (triggers DiskSpaceLow)
dd if=/dev/zero of=/tmp/bigfile bs=1M count=1000

# Clean up after test
rm /tmp/bigfile
```

### Method 4: Python CPU Stress
```bash
# Create CPU stress script
cat > stress_test.py << EOF
import time
import threading

def cpu_stress():
    while True:
        pass

for i in range(4):
    t = threading.Thread(target=cpu_stress)
    t.daemon = True
    t.start()

time.sleep(300)
EOF

python3 stress_test.py
```

## Expected Alert Timeline
1. **Condition Met** → Prometheus detects threshold breach
2. **Pending State** → Waits for duration (1-2 minutes)
3. **Firing State** → Sends alert to Alertmanager
4. **Notification Sent** → Email and Slack notification delivered

## Troubleshooting Alerts

```bash
# Check alert status in Prometheus
# Go to http://<EC2-IP>:9090/alerts

# Check alertmanager logs
docker-compose logs alertmanager

# Verify configuration
docker-compose config

# Restart alertmanager only
docker-compose restart alertmanager
```

## Common Issues
- Port 9093 not open in security group
- Gmail app password incorrect
- Slack webhook URL invalid
- Container hostname in URLs (use EC2 IP instead)

## Configuration Files Summary

```
Guided-Monitoring-Lab/
├── compose.yml          # Added alertmanager service
├── prometheus.yml       # Added alerting and rules
├── alertmanager.yml     # Notification routing
├── alert-rules.yml      # Alert conditions
└── ALERTING.md         # This documentation
```