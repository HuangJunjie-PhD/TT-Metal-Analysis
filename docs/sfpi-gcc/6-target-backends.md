---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc/6-target-backends
---

# Target Backends

Relevant source files
*   [gcc/config/aarch64/aarch64-arches.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-arches.def)
*   [gcc/config/aarch64/aarch64-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-builtins.cc)
*   [gcc/config/aarch64/aarch64-c.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-c.cc)
*   [gcc/config/aarch64/aarch64-option-extensions.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-option-extensions.def)
*   [gcc/config/aarch64/aarch64-protos.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-protos.h)
*   [gcc/config/aarch64/aarch64-simd-builtins.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-simd-builtins.def)
*   [gcc/config/aarch64/aarch64-simd.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-simd.md?plain=1)
*   [gcc/config/aarch64/aarch64-sve.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-sve.md?plain=1)
*   [gcc/config/aarch64/aarch64-tuning-flags.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-tuning-flags.def)
*   [gcc/config/aarch64/aarch64.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64.cc)
*   [gcc/config/aarch64/aarch64.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64.h)
*   [gcc/config/aarch64/aarch64.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64.md?plain=1)
*   [gcc/config/aarch64/aarch64.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64.opt)
*   [gcc/config/aarch64/arm_acle.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/arm_acle.h)
*   [gcc/config/aarch64/arm_neon.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/arm_neon.h)
*   [gcc/config/aarch64/constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/constraints.md?plain=1)
*   [gcc/config/aarch64/iterators.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/iterators.md?plain=1)
*   [gcc/config/i386/avx512fp16intrin.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/avx512fp16intrin.h)
*   [gcc/config/i386/avx512fp16vlintrin.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/avx512fp16vlintrin.h)
*   [gcc/config/i386/i386-builtin-types.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386-builtin-types.def)
*   [gcc/config/i386/i386-builtin.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386-builtin.def)
*   [gcc/config/i386/i386-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386-builtins.cc)
*   [gcc/config/i386/i386.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386.h)
*   [gcc/config/i386/i386.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386.md?plain=1)
*   [gcc/config/i386/mmx.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/mmx.md?plain=1)
*   [gcc/config/i386/predicates.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/predicates.md?plain=1)
*   [gcc/config/i386/sse.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/sse.md?plain=1)
*   [gcc/config/i386/x86-tune-costs.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/x86-tune-costs.h)
*   [gcc/config/i386/x86-tune.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/x86-tune.def)
*   [gcc/config/riscv/constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/constraints.md?plain=1)
*   [gcc/config/riscv/riscv-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc)
*   [gcc/config/riscv/riscv-ftypes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def)
*   [gcc/config/rs6000/altivec.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/altivec.md?plain=1)
*   [gcc/config/rs6000/constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/constraints.md?plain=1)
*   [gcc/config/rs6000/dfp.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/dfp.md?plain=1)
*   [gcc/config/rs6000/mma.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/mma.md?plain=1)
*   [gcc/config/rs6000/predicates.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/predicates.md?plain=1)
*   [gcc/config/rs6000/rs6000-protos.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-protos.h)
*   [gcc/config/rs6000/rs6000.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.md?plain=1)
*   [gcc/config/rs6000/rs6000.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.opt)
*   [gcc/config/rs6000/sync.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/sync.md?plain=1)
*   [gcc/config/rs6000/vector.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vector.md?plain=1)
*   [gcc/config/rs6000/vsx.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1)
*   [gcc/testsuite/g++.dg/opt/pr104681.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/opt/pr104681.C)
*   [gcc/testsuite/g++.dg/vect/pr105254.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/vect/pr105254.cc)
*   [gcc/testsuite/g++.target/i386/pr98335.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/i386/pr98335.C)
*   [gcc/testsuite/gcc.c-torture/compile/pr100305.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.c-torture/compile/pr100305.c)
*   [gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-1.c)
*   [gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-2.c)
*   [gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-3.c)
*   [gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-4.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/costmodel/x86_64/costmodel-pr104582-4.c)
*   [gcc/testsuite/gcc.target/aarch64/acle/data-intrinsics.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/acle/data-intrinsics.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-1.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-2.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-3.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-4.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-4.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-5.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-5.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-6.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-6.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-7.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-7.c)
*   [gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-8.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/advsimd-intrinsics/vshl-opt-8.c)
*   [gcc/testsuite/gcc.target/aarch64/lane-bound-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/lane-bound-1.c)
*   [gcc/testsuite/gcc.target/aarch64/lane-bound-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/lane-bound-2.c)
*   [gcc/testsuite/gcc.target/aarch64/mops_1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/mops_1.c)
*   [gcc/testsuite/gcc.target/aarch64/mops_2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/mops_2.c)
*   [gcc/testsuite/gcc.target/aarch64/mops_3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/mops_3.c)
*   [gcc/testsuite/gcc.target/aarch64/mops_4.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/mops_4.c)
*   [gcc/testsuite/gcc.target/aarch64/narrow_zero_high_half.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/narrow_zero_high_half.c)
*   [gcc/testsuite/gcc.target/aarch64/pr100056.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/pr100056.c)
*   [gcc/testsuite/gcc.target/aarch64/pr103085.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/pr103085.c)
*   [gcc/testsuite/gcc.target/aarch64/simd/lowering_tbaa.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/simd/lowering_tbaa.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/cost_model_14.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/cost_model_14.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/cse_sve_vl_constants_1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/cse_sve_vl_constants_1.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/pr100048.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/pr100048.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/pr98657.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/pr98657.c)
*   [gcc/testsuite/gcc.target/aarch64/vec-init-12.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vec-init-12.c)
*   [gcc/testsuite/gcc.target/aarch64/vector_structure_intrinsics.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vector_structure_intrinsics.c)
*   [gcc/testsuite/gcc.target/aarch64/vsqrt-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vsqrt-1.c)
*   [gcc/testsuite/gcc.target/aarch64/vsqrt-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vsqrt-2.c)
*   [gcc/testsuite/gcc.target/i386/avx-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/avx-1.c)
*   [gcc/testsuite/gcc.target/i386/pr101044.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101044.c)
*   [gcc/testsuite/gcc.target/i386/pr101424.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101424.c)
*   [gcc/testsuite/gcc.target/i386/pr101796-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101796-1.c)
*   [gcc/testsuite/gcc.target/i386/pr101900-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101900-1.c)
*   [gcc/testsuite/gcc.target/i386/pr101900-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101900-2.c)
*   [gcc/testsuite/gcc.target/i386/pr101900-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101900-3.c)
*   [gcc/testsuite/gcc.target/i386/pr101989-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr101989-3.c)
*   [gcc/testsuite/gcc.target/i386/pr102543.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr102543.c)
*   [gcc/testsuite/gcc.target/i386/pr104188.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr104188.c)
*   [gcc/testsuite/gcc.target/i386/pr91446.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr91446.c)
*   [gcc/testsuite/gcc.target/i386/pr92658-avx512bw-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr92658-avx512bw-2.c)
*   [gcc/testsuite/gcc.target/i386/pr98335.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr98335.c)
*   [gcc/testsuite/gcc.target/i386/pr99881.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/pr99881.c)
*   [gcc/testsuite/gcc.target/i386/sse-13.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/sse-13.c)
*   [gcc/testsuite/gcc.target/i386/sse-14.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/sse-14.c)
*   [gcc/testsuite/gcc.target/i386/sse-22.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/sse-22.c)
*   [gcc/testsuite/gcc.target/i386/sse-23.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/sse-23.c)
*   [gcc/testsuite/gcc.target/powerpc/int_128bit-runnable.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/int_128bit-runnable.c)
*   [gcc/testsuite/gcc.target/powerpc/mffscrni_p9.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mffscrni_p9.c)
*   [gcc/testsuite/gcc.target/powerpc/mma-builtin-10-pair.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mma-builtin-10-pair.c)
*   [gcc/testsuite/gcc.target/powerpc/mma-builtin-10-quad.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mma-builtin-10-quad.c)
*   [gcc/testsuite/gcc.target/powerpc/mma-builtin-6.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mma-builtin-6.c)
*   [gcc/testsuite/gcc.target/powerpc/pr102239.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr102239.c)
*   [gcc/testsuite/gcc.target/powerpc/pr102976.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr102976.c)
*   [gcc/testsuite/gcc.target/powerpc/pr104923.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr104923.c)
*   [gcc/testsuite/gcc.target/powerpc/pr105991.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr105991.c)
*   [gcc/testsuite/gcc.target/powerpc/pr94613.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr94613.c)
*   [gcc/testsuite/gcc.target/powerpc/sldoi_to_mov.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/sldoi_to_mov.c)
*   [gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v16qi.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v16qi.c)
*   [gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v4sf.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v4sf.c)
*   [gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v4si.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec-splat-constant-v4si.c)
*   [gcc/testsuite/gcc.target/powerpc/vec_reve_1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec_reve_1.c)
*   [gcc/testsuite/gcc.target/powerpc/vec_reve_2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec_reve_2.c)

This document covers the target architecture backends supported by sfpi-gcc. Target backends are responsible for translating GCC's intermediate representation into target-specific machine code, including instruction selection, register allocation, and code generation. For information about SFPU-specific extensions, see [SFPU Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/2-sfpu-extensions).

The major target backends in sfpi-gcc include x86/i386, AArch64, and PowerPC/RS6000, each with comprehensive support for vector instructions and SIMD operations that are essential for high-performance computing workloads.

## Backend Architecture Overview

GCC's target backend system uses machine descriptions (.md files) to define instruction patterns, register constraints, and code generation rules. Each backend consists of several key components:

Sources: [gcc/config/i386/i386.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386.md?plain=1#L1-L50)[gcc/config/aarch64/aarch64.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64.md?plain=1#L1-L50)[gcc/config/rs6000/rs6000.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.md?plain=1#L1-L50)

## Backend Implementation Structure

Each target backend follows a consistent organizational structure within the `gcc/config/` directory:

| Component | x86/i386 | AArch64 | PowerPC/RS6000 |
| --- | --- | --- | --- |
| Main MD File | `i386.md` | `aarch64.md` | `rs6000.md` |
| Vector MD | `sse.md`, `mmx.md` | `aarch64-simd.md` | `altivec.md`, `vsx.md` |
| Target Header | `i386.h` | `aarch64-protos.h` | `rs6000-protos.h` |
| Implementation | `i386.cc` | `aarch64.cc` | `rs6000.cc` |
| Builtins | `i386-builtin.def` | `aarch64-simd-builtins.def` | `rs6000-builtin.def` |

Sources: [gcc/config/i386/i386.h 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386.h#L1-L50)[gcc/config/aarch64/aarch64-protos.h 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-protos.h#L1-L50)[gcc/config/rs6000/rs6000.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.md?plain=1#L1-L50)

## x86/i386 Backend

The x86 backend provides comprehensive support for Intel and AMD processors, including extensive SIMD instruction sets from MMX through AVX-512.

### Vector Instruction Support

The x86 backend supports multiple vector instruction set architectures:

The SSE machine description defines comprehensive vector mode iterators for different instruction sets:

```
;; All vector modes including V?TImode, used in move patterns.
(define_mode_iterator VMOVE
  [(V64QI "TARGET_AVX512F") (V32QI "TARGET_AVX") V16QI
   (V32HI "TARGET_AVX512F") (V16HI "TARGET_AVX") V8HI
   (V16SI "TARGET_AVX512F") (V8SI "TARGET_AVX") V4SI
   (V8DI "TARGET_AVX512F")  (V4DI "TARGET_AVX") V2DI])
```

Sources: [gcc/config/i386/sse.md 227-236](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/sse.md?plain=1#L227-L236)[gcc/config/i386/mmx.md 47-52](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/mmx.md?plain=1#L47-L52)[gcc/config/i386/avx512fp16intrin.h 38-39](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/avx512fp16intrin.h#L38-L39)

### Built-in Functions and Intrinsics

The x86 backend provides extensive built-in function support through systematic macro definitions:

Sources: [gcc/config/i386/i386-builtin.def 25-28](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/i386-builtin.def#L25-L28)[gcc/config/i386/avx512fp16intrin.h 38-39](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/avx512fp16intrin.h#L38-L39)

## AArch64 Backend

The AArch64 backend supports ARM's 64-bit architecture with comprehensive NEON SIMD instruction support.

### NEON SIMD Support

The AArch64 backend provides extensive NEON vector support through systematic type definitions and intrinsics:

The NEON intrinsics header defines comprehensive vector types:

`typedef __Int8x8_t int8x8_t;typedef __Int16x4_t int16x4_t;typedef __Int32x2_t int32x2_t;typedef __Float32x2_t float32x2_t;typedef __Int8x16_t int8x16_t;typedef __Int16x8_t int16x8_t;typedef __Int32x4_t int32x4_t;typedef __Float32x4_t float32x4_t;`
Sources: [gcc/config/aarch64/arm_neon.h 40-58](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/arm_neon.h#L40-L58)[gcc/config/aarch64/aarch64-simd.md 21-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-simd.md?plain=1#L21-L50)

### SIMD Built-in Functions

The AArch64 backend uses a systematic approach to define SIMD built-ins through macro-based definitions:

Sources: [gcc/config/aarch64/aarch64-simd-builtins.def 46-60](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-simd-builtins.def#L46-L60)

## PowerPC/RS6000 Backend

The PowerPC backend supports IBM POWER processors and PowerPC architectures with AltiVec and VSX vector instruction sets.

### Vector Instruction Set Support

The PowerPC backend supports multiple vector instruction set architectures:

The VSX machine description defines mode iterators for vector operations:

```
;; Iterator for both scalar and vector floating point types supported by VSX
(define_mode_iterator VSX_B [DF V4SF V2DF])

;; Iterator for the 2 64-bit vector types
(define_mode_iterator VSX_D [V2DF V2DI])

;; Iterator for vector floating point types supported by VSX
(define_mode_iterator VSX_F [V4SF V2DF])
```

Sources: [gcc/config/rs6000/vsx.md 28-47](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1#L28-L47)[gcc/config/rs6000/altivec.md 1-15](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/altivec.md?plain=1#L1-L15)[gcc/config/rs6000/rs6000.md 27-54](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.md?plain=1#L27-L54)

### VSX and AltiVec Integration

The PowerPC backend integrates AltiVec and VSX instruction sets through a unified register and mode system:

Sources: [gcc/config/rs6000/vsx.md 80-106](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1#L80-L106)[gcc/config/rs6000/vsx.md 174-187](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1#L174-L187)

## Machine Description Patterns

All backends use machine description (.md) files to define instruction patterns, constraints, and code generation rules.

### Instruction Pattern Structure

Machine descriptions use a consistent pattern structure across all backends:

Sources: [gcc/config/i386/sse.md 20-106](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/sse.md?plain=1#L20-L106)[gcc/config/aarch64/aarch64-simd.md 21-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-simd.md?plain=1#L21-L50)[gcc/config/rs6000/vsx.md 22-47](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1#L22-L47)

## Cross-Backend Vector Support Comparison

The three major backends provide different approaches to vector instruction support:

| Feature | x86/i386 | AArch64 | PowerPC |
| --- | --- | --- | --- |
| Vector Sizes | 64, 128, 256, 512-bit | 64, 128-bit | 128-bit |
| Primary ISA | SSE/AVX/AVX-512 | NEON | AltiVec/VSX |
| Mode Iterators | `VMOVE`, `V48_AVX512VL` | `VALL_F16`, `VDQ_I` | `VSX_F`, `VSX_L` |
| Register Classes | `xmm`, `ymm`, `zmm` | `w`, `v` | `wa`, `v` |
| Built-in Prefix | `__builtin_ia32_` | `__builtin_aarch64_` | `__builtin_altivec_` |

Sources: [gcc/config/i386/sse.md 227-260](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/i386/sse.md?plain=1#L227-L260)[gcc/config/aarch64/iterators.md 64-120](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/iterators.md?plain=1#L64-L120)[gcc/config/rs6000/vsx.md 149-172](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/vsx.md?plain=1#L149-L172)

Dismiss
Refresh this wiki

Enter email to refresh