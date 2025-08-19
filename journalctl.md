# Journalctl

## Check for kernel hangs
```bash
sudo journalctl -k -b -1 -n 2000 -f
```

## Check network log
```bash
sudo journalctl -lu systemd-networkd
```

## Check service log
```bash
sudo journalctl -e -u roche-htp-nsa
```
