# Dpkg

## Check deb package dependency info

```bash
dpkg -I ./fastq-stats.deb
```

## List files installed with the deb

```bash
dpkg -L fastq-stats
```

## Reconfigure

When different versions are installed or a kernel needs to be rebuilt, you can reconfigure the installed package without having to fully reinstall.

```bash
sudo dpkg-reconfigure cuda-toolkit-11-7
```
