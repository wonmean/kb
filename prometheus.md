# Prometheus

## Install Prometheus
Prometheus and node exporter are necessary for machine metrics.  
Install using apt, since the package can setup all the dependencies and service files.  
Manually upgrade both prometheus and node exporter so that they are compatible with the latest version of Grafana.  
Update service arguments and restart services.
```bash
sudo apt -yqq install prometheus prometheus-node-exporter \
&& sudo systemctl stop prometheus prometheus-node-exporter \
&& wget -q https://github.com/prometheus/prometheus/releases/download/v2.47.2/prometheus-2.47.2.linux-amd64.tar.gz \
&& wget -q https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz \
&& tar -xzf node_exporter-1.6.1.linux-amd64.tar.gz \
&& tar -xzf prometheus-2.47.2.linux-amd64.tar.gz \
&& sudo cp prometheus-2.47.2.linux-amd64/prometheus /usr/bin/prometheus \
&& sudo cp prometheus-2.47.2.linux-amd64/promtool /usr/bin/promtool \
&& sudo cp node_exporter-1.6.1.linux-amd64/node_exporter /usr/bin/prometheus-node-exporter \
&& sudo cp -r prometheus-2.47.2.linux-amd64/consoles /etc/prometheus \
&& sudo cp -r prometheus-2.47.2.linux-amd64/console_libraries /etc/prometheus \
&& echo -e "ARGS=\"--config.file /etc/prometheus/prometheus.yml \\n\
    --storage.tsdb.path /var/lib/prometheus/ \\n\
    --web.console.templates=/etc/prometheus/consoles \\n\
    --web.console.libraries=/etc/prometheus/console_libraries\"" \
    | sudo tee /etc/prometheus/prometheus \
&& sudo sed -i "s/default/prometheus/g" /lib/systemd/system/prometheus.service \
&& sudo sed -i "s/15s/1s/g;s/5s/1s/g" /etc/prometheus/prometheus.yml \
&& sudo systemctl daemon-reload \
&& sudo systemctl start prometheus prometheus-node-exporter \
&& sleep 5 \
&& sudo systemctl list-units -q prometheus.service prometheus-node-exporter.service \
&& rm -r prometheus-2.47.2.linux-amd64 prometheus-2.47.2.linux-amd64.tar.gz \
&& rm -r node_exporter-1.6.1.linux-amd64 node_exporter-1.6.1.linux-amd64.tar.gz
```

## Install Grafana
On a single system that will host the dashboards.
```bash
wget -q -O - https://apt.grafana.com/gpg.key \
| gpg --dearmor \
| sudo tee /usr/share/keyrings/grafana.gpg >/dev/null \
&& echo "deb [signed-by=/usr/share/keyrings/grafana.gpg] https://apt.grafana.com stable main" \
| sudo tee /etc/apt/sources.list.d/grafana.list >/dev/null \
&& sudo apt update \
&& sudo apt -yqq install grafana \
&& sudo systemctl daemon-reload \
&& sudo systemctl enable grafana-server \
&& sudo systemctl start grafana-server
```
Default password is admin / admin.

## Ignore netclass on systems with transient devices
This was recorded on the Digital Pathology DGX system (rscusl42rtdai).
```text
Feb  8 10:30:46 rscusl42rtdai prometheus-node-exporter[1105257]: time="2024-02-08T10:30:46-08:00" level=error msg="ERROR: netclass collector failed after 0.066041s: could not get net class info: error obtaining net class info: failed to read file \"/sys/class/net/ibp12s0/create_child\": open /sys/class/net/ibp12s0/create_child: permission denied" source="collector.go:132"
```

This was fixed by ignoring that particular device for netclass.  
Add the `--collector.netclass.ignored-devices="^ibp12s0$"` for `/etc/systemd/system/multi-user.target.wants/prometheus-node-exporter.service`.  
Other options can be found using `sudo /usr/bin/prometheus-node-exporter --help`.
```bash
ExecStart=/usr/bin/prometheus-node-exporter --collector.netclass.ignored-devices="^ibp12s0$" $ARGS
```

Reload and restart.
```bash
sudo systemctl daemon-reload \
&& sudo systemctl restart prometheus-node-exporter.service
```
