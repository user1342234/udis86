# normalize-x86-64

This branch is used to normalize an x86-64 buffer by:\
    - Replacing rip-relative instructions: `mov rax, [rip+0x12345]` => ` mov rax, [rip+0xffffff]`\
    - Replacing registers: `movzx rdx, eax` => `movzx rax, eax`\
    - Replacing memory references: `mov rax, [rbp-0x8]` => `mov rax, [rbx-0x1]`\
    *Note: The displacement 0x8 has been replaced/normalized with 0xff which is then interpreted as -1.

## Purpose
The normalization is precursory step to compare simliarities of functions using by their bytes. By normalizing these functions, rip-relative instructions, memory references, and register names are all standardized to specific values.

```asm
mov rax, rdx
movzx rbx, bl
movzx rcx, al
jmp   qword ptr [rip + 0x19]
```
=>

```asm
mov rax, rax
movzx rax, al
movzx rax, al
jmp qword [rip+0xff]
```

The register normals (r64, r8, etc) are written to their families first register. 
`xmm0,xmm1,...->xmm0`\
`rax,rdx,rbx,...->rax`\
`r8,r9,r10,...->r8`\
`eax,edx,esi,...->eax`\
`ah,bh,...->ah`\
`al,dl,...->al`

## Caveats
Our standardization loses some information, particularly data dependencies between instructions:\
```add rbx, rdx``` => ```add rax,rax```\
```mov rdx, rax``` => ```mov rax, rax```

## Building

### 64-bit
```
cmake -G "Visual Studio 16 2019" -A x64 -B build
```
