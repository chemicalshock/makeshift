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
BUILD_PROFILES := host

PROFILE_host_ARCH := m64
PROFILE_host_CXXFLAGS := -ffreestanding

include makeshift.mk
```

Global `USER_*` variables still apply to every profile. `PROFILE_<name>_*`
variables are additive overrides layered on top.

## Example: kernel + user profiles

```make
BUILD_EXE := 0
BUILD_STATIC := 1
BUILD_SHARED := 1

BUILD_PROFILES := kernel user

PROFILE_kernel_BUILD_SHARED := 0
PROFILE_kernel_ARCH := m64
PROFILE_kernel_CXXFLAGS := -ffreestanding -mcmodel=kernel -mno-red-zone

PROFILE_user_BUILD_STATIC := 0
PROFILE_user_ARCH := m64
PROFILE_user_SHARED_LDFLAGS := -Wl,-soname,libmylib.so

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
    │   ├── depinc/   # dependency include symlink map (shared across profiles)
    │   └── <profile>/ # optional per-profile objects/deps
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

- `make` or `make all`: build enabled target/profile combinations
- `make exe`: build executables for all enabled profiles
- `make static`: build static libraries for all enabled profiles
- `make shared`: build shared libraries for all enabled profiles
- `make exe-<profile>`: build one profile's executable
- `make static-<profile>`: build one profile's static library
- `make shared-<profile>`: build one profile's shared library
- `make ut`: run unit tests if `src/tst/ut/Makefile` exists
- `make sy`: run system tests if `src/tst/sy/runner` exists
- `make clean`: clean build artefacts and test artefacts

By default, objects go to `src/bld/<profile>/` and final artefacts go to
`bld/<profile>/`.

## Build options

- `MODE=debug|release` (default: `debug`)
- `BUILD_PROFILES="name..."` enable named build profiles
- `BUILD_M32=0|1` enable the 32-bit build graph (default: `0`)
- `BUILD_M64=0|1` enable the 64-bit build graph (default: `1`)
- `VERBOSE=1` for full command output
- `CXX_STD=<standard>` (default: `c++20`)

`BUILD_PROFILES` is the preferred interface. When it is not set, `makeshift`
still supports the legacy `BUILD_M32` / `BUILD_M64` flow and treats `m32` and
`m64` as compatibility profile names.

## Profile variables

For a profile named `kernel`, the following knobs are available:

- `PROFILE_kernel_BUILD_EXE`, `PROFILE_kernel_BUILD_STATIC`, `PROFILE_kernel_BUILD_SHARED`
- `PROFILE_kernel_CPPFLAGS`, `PROFILE_kernel_CXXFLAGS`
- `PROFILE_kernel_SFLAGS`, `PROFILE_kernel_ASMFLAGS`
- `PROFILE_kernel_S_PICFLAGS`, `PROFILE_kernel_ASM_PICFLAGS`
- `PROFILE_kernel_LDFLAGS`, `PROFILE_kernel_EXE_LDFLAGS`, `PROFILE_kernel_SHARED_LDFLAGS`
- `PROFILE_kernel_LDLIBS`, `PROFILE_kernel_EXE_LDLIBS`, `PROFILE_kernel_SHARED_LDLIBS`
- `PROFILE_kernel_ARCH`
- `PROFILE_kernel_ARCHFLAGS`, `PROFILE_kernel_EXE_ARCHFLAGS`, `PROFILE_kernel_SHARED_ARCHFLAGS`
- `PROFILE_kernel_EXE_LINKER`, `PROFILE_kernel_SHARED_LINKER`
- `PROFILE_kernel_EXEC_OUT`, `PROFILE_kernel_STATIC_OUT`, `PROFILE_kernel_SHARED_OUT`

`PROFILE_<name>_ARCH := m32|m64` enables the same automatic arch-flag filtering
and linker compatibility behavior that the legacy `m32`/`m64` variants used.

## Output overrides

When exactly one profile is enabled, the legacy `EXEC_OUT`, `STATIC_OUT`, and
`SHARED_OUT` overrides still apply.

For multiple profiles, prefer:

- `PROFILE_<name>_EXEC_OUT`
- `PROFILE_<name>_STATIC_OUT`
- `PROFILE_<name>_SHARED_OUT`

The older `EXEC_OUT_<name>`, `STATIC_OUT_<name>`, and `SHARED_OUT_<name>`
aliases are still accepted for compatibility.
