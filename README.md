# normalize-x86-64

This branch is used to normalize an x86-64 buffer by:\
    - Replacing rip-relative instructions: `mov eax, [rip+12345h]` => `mov eax, [rip+0x1]`\
    - Replacing registers: `movzx rdx, eax` => `movzx rax, eax`

Example:
```asm
mov rax, rdx     
movzx rbx, al       
movzx rsi, dl
```
=> 
```asm
mov rax, rax
movzx rax, al
movzx rax, al
```

## Purpose
The normalization is precursory step to compare simliarities of functions using by their bytes. By normalizing these functions, their bytes become standardized but still maintain information like below:

```asm
mov rax, rdx
movzx rbx, eax
movzx rbx, al
jmp qword ptr [rip+19h]
```
=>

```asm
mov r64, r64
movzx r64 , r32
movzx r64, r8
jmp qword ptr [rip+1h]*
```
*Note: jmp/call instructions offsets are changed into section references. For example, `jmp` to `.text` could be offset `0x1`. `jmp` to an import could be offset `0x2`. Offsets are chosen incrementally when parsing sections.
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
