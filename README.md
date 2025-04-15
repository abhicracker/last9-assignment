# 🚀 Monitoring Implementation: OpenTelemetry + Grafana vs Datadog

This project documents the end-to-end implementation of a **dual-monitoring setup** using:

- 📦 **Part 1:** OpenTelemetry Collector + Prometheus + Loki + Grafana
- 🐶 **Part 2 (Stretch Goal):** Datadog Agent for Logs & Metrics

---

## 📁 Project Structure

/etc/ 
  ── otelcol/ 
    └── config.yaml 
  ── prometheus/ 
    │ └── prometheus.yml 
├── systemd/ 
     ├── prometheus.service 
  │ └── otelcol.service 

/var/log/nginx/ ├── access.log ├── access.log.1 ├── error.log └── error.log.1


## 🧩 Part 1: OpenTelemetry + Grafana Implementation

### ✅ Steps Performed

1. **Installed OpenTelemetry Collector**
2. **Configured `config.yaml`** to:
   - Scrape Node Exporter & self metrics
   - Forward logs from `/var/log/nginx/*.log`
   - Export to Prometheus & Loki
3. **Set up Prometheus** as a metric exporter
4. **Installed and configured Grafana**
   - Added Prometheus and Loki as data sources
   - Created dashboards for:
     - Host metrics
     - Nginx logs
5. **Enabled Node Exporter**
   - Confirmed data flow into Prometheus

### 🔥 Screenshot Previews
[![Grafana Node Exporter Dashboard](https://i.ibb.co/679Q6fWs/Screenshot-from-2025-04-14-22-05-50.png)](https://abhisheksarkate53.grafana.net/)
![Grafana Host Logs](https://i.ibb.co/YTNbBq3P/Screenshot-from-2025-04-14-22-19-32.png)
*Sample Host Dashboard from Grafana*

---

## 🐶 Part 2: Datadog Implementation

### ✅ Steps Performed

1. **Installed Datadog Agent on server**
2. **Enabled logs and metrics in `datadog.yaml`**
3. **Configured `nginx.d/conf.yaml`**:
   ```yaml
   logs:
     - type: file
       path: /var/log/nginx/access.log*
       service: nginx
       source: nginx
     - type: file
       path: /var/log/nginx/error.log*
       service: nginx
       source: nginx
4. **Restarted Agent and verified ingestion**

### 🔥 Screenshot Previews
[![Grafana DataDog Metrics Dashboard](https://i.ibb.co/srWHysN/Screenshot-from-2025-04-14-22-21-47.png)](https://abhisheksarkate53.grafana.net/)
![Grafana DataDog Logs](https://i.ibb.co/ZRhsrBDc/Screenshot-from-2025-04-14-22-24-07.png)
*Sample Host Dashboard from Grafana*

---
## ⚠️ Challenges Faced & Solutions

| Challenge                        | Solution                                                |
|----------------------------------|----------------------------------------------------------|
| `file_input1` regex mismatch     | Disabled regex parser in `filelog` receiver              |
| Nginx logs not appearing in Datadog | Used wildcard `access.log*` and `error.log*`          |
| Prometheus service failed        | Fixed path to binary using `readlink -f ./prometheus`   |
| Needed to kill otelcol manually  | Created a `systemd` service for it                      |
| Metrics not showing on Grafana   | Verified targets at `/targets`, fixed IPs/ports         |

---

## 🧪 Comparison: Grafana Stack vs Datadog

| Feature              | Grafana Stack                        | Datadog                           |
|----------------------|--------------------------------------|-----------------------------------|
| Setup Complexity     | Moderate (self-managed)              | Simple (one-agent)                |
| Cost                 | Free (self-hosted)                   | Paid (after trial)                |
| Custom Dashboards    | Highly customizable                  | Good, less flexibility            |
| Log Ingestion        | Via Loki                             | Native support, very smooth       |
| Metrics              | Prometheus / Node Exporter           | Native support                    |
| Alerting             | Grafana + Prometheus Alertmanager    | Built-in                          |
| Third-party plugins  | Extensive                            | Limited                           |

---

## 📋 Conclusion

- **Grafana Stack** offers **great flexibility** and is best suited for **cost-conscious setups** and teams with **DevOps expertise**.
- **Datadog** is ideal for **quick deployments**, offering **ease of use** and **out-of-the-box insights** with minimal setup.

# View OpenTelemetry logs
journalctl -u otelcol.service -f

# View Prometheus logs
journalctl -u prometheus.service -f

# Datadog Agent status
sudo datadog-agent status

# Restart services
sudo systemctl restart otelcol
sudo systemctl restart prometheus
sudo systemctl restart datadog-agent

