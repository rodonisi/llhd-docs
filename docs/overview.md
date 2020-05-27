---
title: Overview
layout: default
nav_order: 2
---

Types:

| Old type | New type | Remarks |
|-----------------|-----------------|---------|
| `void` | [`none`](https://mlir.llvm.org/docs/LangRef/#none-type) | |
| `time` | `!llhd.time` | |
| `iN` | [MLIR signless integer](https://mlir.llvm.org/docs/LangRef/#integer-type) | |
| `nN` | | enumeration not yet implemented |
| `lN` | | nine-valued logic not yet implemented |
| `T*` | | pointers not yet implemented |
| `T$` | `!llhd.sig<T>` | only integers for now |
| Array `[N x T]` | [MLIR Vector](https://mlir.llvm.org/docs/LangRef/#vector-type) | |
| Struct `{T0, T1, ...}` | [MLIR Tuple](https://mlir.llvm.org/docs/LangRef/#tuple-type) | |

Instructions:

| Old instruction | New instruction | Remarks |
|-----------------|-----------------|---------|
| **Values** | | |
| `const` | `llhd.const`, `constant` | not stable, might switch to std dialect `constant` for integers |
| `alias` | | removed |
| Array construction | [`constant`](https://mlir.llvm.org/docs/Dialects/Standard/#stdconstant-constantop) | stable, look [here](https://mlir.llvm.org/docs/LangRef/#elements-attributes) for the three possible attributes to create a vector, e.g. `%vec = constant dense<[1,2,3]> : vector<3xi32>` |
| Struct/Tuple construction, `insf`, `inss`, `extf` | | not yet implemented |
| `exts` | [`llhd.exts`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdexts-llhdextsop) | not stable |
| `mux` | [`vector.extractelement`](https://mlir.llvm.org/docs/Dialects/Vector/#vectorextractelement-vectorextractelementop) | stable |
| **Bitwise** | | |
| `not`, `and`, `or`, `xor` | [`llhd.not`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdnot-llhdnotop), [`llhd.and`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdand-llhdandop), [`llhd.or`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdor-llhdorop), [`llhd.xor`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdxor-llhdxorop) | stable, nine-valued logic will be added later |
| `shl`, `shr` | [`llhd.shl`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdshl-llhdshlop), [`llhd.shr`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdshr-llhdshrop) | only integer and signal supported for now |
| **Arithmetic** | | |
| `neg`, `add`, `sub` | [`llhd.neg`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdneg-llhdnegop), [`addi`](https://mlir.llvm.org/docs/Dialects/Standard/#stdaddi-addiop), [`subi`](https://mlir.llvm.org/docs/Dialects/Standard/#stdsubi-subiop) | stable |
| `smul`, `umul` | [`muli`](https://mlir.llvm.org/docs/Dialects/Standard/#stdmuli-muliop) | stable |
| `sdiv`, `udiv` | [`divi_signed`](https://mlir.llvm.org/docs/Dialects/Standard/#stddivi_signed-signeddiviop), [`divi_unsigned`](https://mlir.llvm.org/docs/Dialects/Standard/#stddivi_unsigned-unsigneddiviop) | stable |
| `srem` | [`remi_signed`](https://mlir.llvm.org/docs/Dialects/Standard/#stdremi_signed-signedremiop) | stable |
| `urem`, `umod` | [`remi_unsigned`](https://mlir.llvm.org/docs/Dialects/Standard/#stdremi_unsigned-unsignedremiop) | stable |
| `smod` | [`llhd.smod`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdsmod-llhdsmodop) | stable |
| **Comparison** | | |
| `eq`, `neq`, `slt`, `sle`, `sgt`, `sge`, `ult`, `ule`, `ugt`, `uge` | [`cmpi`](https://mlir.llvm.org/docs/Dialects/Standard/#stdcmpi-cmpiop) | stable, use predicates accordingly, integers only |
| **Control flow** | | |
| `phi` | [MLIR block argument](https://mlir.llvm.org/docs/LangRef/#blocks) | stable, MLIR uses block arguments instead |
| `br` | [`br`](https://mlir.llvm.org/docs/Dialects/Standard/#stdbr-branchop) | stable, unconditional variant |
| `br` | [`cond_br`](https://mlir.llvm.org/docs/Dialects/Standard/#stdcond_br-condbranchop) | stable, conditional variant |
| `call`, `ret`, `wait`, `halt` | [`call`](https://mlir.llvm.org/docs/Dialects/Standard/#stdcall-callop), [`return`](https://mlir.llvm.org/docs/Dialects/Standard/#stdreturn-returnop), [`llhd.wait`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdwait-llhdwaitop), [`llhd.halt`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdhalt-llhdhaltop) | stable |
| **Memory** | | |
| `var`, `ld`, `st` | | not yet implemented |
| **Signals** | | |
| `sig`, `prb`, `drv` | [`llhd.sig`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdsig-llhdsigop), [`llhd.prb`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdprb-llhdprbop), [`llhd.drv`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhddrv-llhddrvop) | stable |
| **Structural** | | |
| `reg` | [`llhd.reg`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdreg-llhdregop) | not quite stable |
| `del`, `con` | | not yet implemented |
| `inst` | [`llhd.inst`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdinst-llhdinstop) | stable |
| **Units** | | |
| `func`, `proc`, `entity` | [`func`](https://mlir.llvm.org/docs/LangRef/#functions), [`llhd.proc`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdproc-llhdprocop), [`llhd.entity`](https://rodonisi.github.io/llhd-docs/docs/LLHDOps.html#llhdentity-llhdentitoyop) | stable |