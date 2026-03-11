# normalize-x86-64

## Purpose
The normalization is precursory step to compare simliarities of functions using by their bytes. By normalizing these functions, their bytes become standardized but still maintain information like below:

```asm
mov rax, rdx
movzx rbx, eax
movzx rbx, al
jmp qword ptr [rip+19h]
```
Turns into:

```asm
mov r64, r64
movzx r64 , r32
movzx r64, r8
jmp qword ptr [rip+1h]*
```
*Note: jmp/call instructions offsets are changed into section references. For example, `jmp` to `.text.` would be offset `0x1`. `jmp` to an import would be offset `0x2`
The register normals (r64, r8, etc) are written to their families first register. So, `xmm0,xmm1,...->xmm0`      `rax,rdx,rbx,...->rax`      `r8,r9,r10,...->r8`     `eax,edx,esi,...->eax`        `ah,bh,...->ah`     `al,dl,...->al`

## Caveats
Our standardization loses information:
```asm add rbx, rdx``` => ```asm add rax,rax```

This branch is used to normalize an x86-64 buffer by:
    - Replacing rip-relative instructions IE. `mov eax, [rip+12345h]` => `mov eax, [rip+fffffh]`
    - Replacing registers IE. `mov rax, rdx     movzx rbx, al       movzx rsi, dl` => `mov rax, rax     movzx rax, al       movzx rax, al`
## Building

### 64-bit
```
cmake -G "Visual Studio 16 2019" -A x64 -B build
```
