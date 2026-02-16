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

- `make` or `make all`: build enabled targets
- `make exe`: build executable (`bld/<BIN_NAME>`)
- `make static`: build static library (`bld/lib<LIB_BASENAME>.a`)
- `make shared`: build shared library (`bld/lib<LIB_BASENAME>.so`)
- `make ut`: run unit tests if `src/tst/ut/Makefile` exists
- `make sy`: run system tests if `src/tst/sy/runner` exists
- `make clean`: clean build artefacts and test artefacts

## Build options

- `MODE=debug|release` (default: `debug`)
- `VERBOSE=1` for full command output
- `CXX_STD=<standard>` (default: `c++20`)
