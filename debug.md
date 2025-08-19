# Debug

## Cuda-dbg
```bash
cuda-gdb --args <app> <options>
(cuda-gdb) set cuda api_failures stop_all
(cuda-gdb) run
(cuda-gdb) exit
```

## Using gdb
GDB cheat sheet is [here](https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf).  
Use the following to peak variables at a breakpoint in VSCode.
```bash
-exec call <expression>
```

## Compute sanitizer
Previously was `cuda-memcheck`.
```bash
compute-sanitizer <app>
compute-sanitizer --tool racecheck <app>
```

## Enable debug info
For use with `nvcc`.
```bash
--debug                                         (-g)                            
        Generate debug information for host code.

--device-debug                                  (-G)                            
        Generate debug information for device code. Turns off all optimizations.
        Don't use for profiling; use -lineinfo instead.

--generate-line-info                            (-lineinfo)                     
        Generate line-number information for device code.

--optimize <level>                              (-O)                            
        Specify optimization level for host code.
```

## Check if a binary has debug info
```bash
objdump --syms ./nsa | grep debug
```

