# AsmMacro.jl

`AsmMacro.jl` provides a relatively simple way to write assembly code in Julia.

## Examples

```julia
# z[1:4] <- x[1:4] + y[1:4]
@asm function add_vec2(x::Ptr{Float64},y::Ptr{Float64},z::Ptr{Float64})
    vmovupd(x[0], ymm0)
    vmovupd(y[0], ymm1)
    vaddpd(ymm0, ymm1, ymm1)
    vmovupd(ymm1, z[0])
end

x = [1.0,2.0,3.0,4.0]
y = [4.0,3.0,2.0,1.0]
z = similar(x)
add_vec2(pointer(x),pointer(y),pointer(z))

julia> z
4-element Array{Float64,1}:
 5.0
 5.0
 5.0
 5.0
```


```julia
# z[1:2] <- x[1:2]*n (with a loop)
@asm function add_loop_vec2(x::Ptr{Float64},n::Int,z::Ptr{Float64})
    movq(n, rcx)
    movapd(x[0], xmm0)
    xorpd(xmm1,xmm1)
    @loop
    addpd(xmm0,xmm1)
    dec(rcx)
    jnz(@loop)
    movapd(xmm1, z[0])
end

x = [1.0,2.0]
n = 10
z = similar(x)
add_loop_vec2(pointer(x),n,pointer(z))

julia> z
2-element Array{Float64,1}:
 10.0
 20.0
```

## Acknowledgement

This package is based on the original code by [Oscar Blumberg](https://github.com/carnaval).
