# Systemctl

## Start and stop services

```bash
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl restart <service>
```

## Edit service environment variables

```bash
sudo systemctl edit <service>
```

Add the following to the `/etc/systemd/system/<service>.service.d/override.conf`.

```text
[Service]
Environment="CUDA_VISIBLE_DEVICES=1,2"
```

## Check service log

```bash
sudo journalctl -e -u <service>
```
