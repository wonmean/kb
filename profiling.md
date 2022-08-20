# Profiling

## Profile memory

Install valgrind and massif-visualizer.

```bash
sudo apt install -y valgrind massif-visualizer
```

Run valgrind using massif.

```bash
valgrind --tool=massif <program>
```

Run `massif-visualizer` on vncserver.

## Profile CPU

```bash
valgrind --tool=callgrind <app>
```

Run `kcachegrind` on vncserver.

## Profile GPU

Install Nsight Systems on laptop, run via SSH on remote workstation.  
Alternatively, you can run via `nsys`.

```bash
nsys profile --stats=true --force-overwrite=true -o report <app>
```

## CUDA memcheck

```bash
cuda-memcheck <app>
cuda-memcheck --tool racecheck <app>
```

## Check IO speed

```bash
iostat
```

## Check network speed

```bash
sudo apt install iperf
iperf -s
iperf -c arcturus
```
