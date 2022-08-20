# Debug

## Enable debug info

For use with `nvcc`.

```text
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

One way to specify build types in the `CMake` file.

```cmake
    if (CMAKE_BUILD_TYPE MATCHES "Release")
        set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -O3 -g -lineinfo)
    elseif (CMAKE_BUILD_TYPE MATCHES "Debug")
        set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -O0 -g -G)
    elseif (CMAKE_BUILD_TYPE MATCHES "RelWithDbgInfo")
        set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -O2 -g -G)
    endif ()
```

## Check if a binary has debug info

```bash
objdump --syms <app> | grep debug
```
