# Monitoring

## Run MOTD again
```bash
sudo run-parts /etc/update-motd.d
```

## CPU monitoring
```bash
sar -u 1
```
If not available, install `sysstat`.

## GPU monitoring
Monitor power, temperature, and utilization at 1 second interval.
```bash
nvidia-smi --query-gpu=timestamp,name,pci.bus_id,temperature.gpu,power.draw,utilization.gpu,utilization.memory --format=csv -l 1
```
Same at 100 ms interval.
```bash
nvidia-smi --query-gpu=timestamp,name,pci.bus_id,temperature.gpu,power.draw,utilization.gpu,utilization.memory --format=csv -lms 100
```
If 1 second interval is okay, `dmon` is also a good option.
```bash
nvidia-smi dmon -s puct
```

## Memory monitoring
```bash
while true; do vmstat >> vmstat.log; sleep 1 ; done
```

## Disk I/O monitoring
```bash
sar -b 1
```

## Network monitoring
Install `net-tools`.
```bash
watch -n 1 sh -c "netstat -W | egrep tcp"
```

## Port monitoring
```bash
sudo lsof -i -P -n | grep rclone
```
To continuously monitor used port count.
```bash
sudo watch -n 1 "lsof -i -P -n | grep rclone | wc -l"
```
