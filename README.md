# udis86

This repository is a fork of [udis86](https://github.com/vmt/udis86) meant for easy builds. It uses [CMake](https://cmake.org) as a build generator.

Current branches:
- `master` :- Base for building other branches
- `normalize-x86-64` :- Normalizing x86-64 assembly code 

## Building

### 64-bit
```
cmake -G "Visual Studio 16 2019" -A x64 -B build
```
