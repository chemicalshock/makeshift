# makeshift

`makeshift` is a template build Makefile for C++ projects. It provides a consistent project layout and common build targets for:

- executables
- static libraries
- shared libraries
- unit/system test hooks

It is designed to be included from your project `Makefile`, where you set project-specific variables.

## Minimal project Makefile

Create a `Makefile` at your project root:

```make
BIN_NAME := app
LIB_BASENAME := mylib

BUILD_EXE := 1
BUILD_STATIC := 1
BUILD_SHARED := 0
BUILD_M32 := 0
BUILD_M64 := 1

include makeshift.mk
```

## Required folder structure

`makeshift.mk` expects this layout (relative to project root):

```text
.
├── Makefile
├── makeshift.mk
└── src
    ├── bin/        # executable .cpp entry points
    ├── lib/        # library/source implementation (.cpp, .c, .S)
    ├── inc/        # project headers
    ├── bld/        # object/dependency output (auto-managed)
    │   ├── depinc/ # dependency include symlink map (shared across ABIs)
    │   ├── m32/    # optional 32-bit objects/deps
    │   └── m64/    # optional 64-bit objects/deps
    └── tst/
        ├── ut/     # optional unit tests (own Makefile + run target)
        ├── sy/     # optional system test runner executable: runner
        └── bld/    # optional test build output
```

Optional dependency includes can be added under:

```text
dep/<dependency>/src/inc
```

These are auto-exposed during compilation.

## Common targets

- `make` or `make all`: build enabled target/ABI combinations
- `make exe`: build executable for all enabled ABIs (`bld/m32/<BIN_NAME>`, `bld/m64/<BIN_NAME>`)
- `make static`: build static library for all enabled ABIs (`bld/m32/lib<LIB_BASENAME>.a`, `bld/m64/lib<LIB_BASENAME>.a`)
- `make shared`: build shared library for all enabled ABIs (`bld/m32/lib<LIB_BASENAME>.so`, `bld/m64/lib<LIB_BASENAME>.so`)
- `make exe-m32 BUILD_M32=1`: build only the 32-bit executable variant
- `make exe-m64 BUILD_M64=1`: build only the 64-bit executable variant
- `make ut`: run unit tests if `src/tst/ut/Makefile` exists
- `make sy`: run system tests if `src/tst/sy/runner` exists
- `make clean`: clean build artefacts and test artefacts

## Build options

- `MODE=debug|release` (default: `debug`)
- `BUILD_M32=0|1` enable the 32-bit build graph (default: `0`)
- `BUILD_M64=0|1` enable the 64-bit build graph (default: `1`)
- `VERBOSE=1` for full command output
- `CXX_STD=<standard>` (default: `c++20`)

When exactly one ABI is enabled, the legacy `EXEC_OUT`, `STATIC_OUT`, and `SHARED_OUT` overrides still apply. For multi-ABI builds, use `EXEC_OUT_m32` / `EXEC_OUT_m64`, `STATIC_OUT_m32` / `STATIC_OUT_m64`, and `SHARED_OUT_m32` / `SHARED_OUT_m64` instead.
