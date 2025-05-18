# Makeshift Unit Test Build System

This project provides a modular, dependency-aware Makefile include (`makeshift.mk`) designed to manage the compilation and execution of C++ unit tests. It supports automatic dependency resolution, visualisation, and execution of test binaries in a clean and extendable way.

## 🛠️ Project Structure

- `makeshift.mk`: Core Makefile include with dependency resolution and target generation.
- `Makefile`: Your main Makefile should include `makeshift.mk`.
- `src/lib`: Contains C++ modules and implementation files.
- `src/tst`: Contains unit tests.
- `bld/`: Default build directory for compiled binaries.

## 🔧 Defining Tests

Each test binary is declared using variables in your test-specific `Makefile`, e.g.:

```make
X86CPPTARGET += TEST_my_module
TEST_my_module,SRCS = test_my_module.cpp
TEST_my_module,USRLIBS = libDep1.cpp libDep2.cpp
TEST_my_module,CPPFLAGS += -I$(ROOT_DIR)/include
```

Each test can list dependent source files via `USRLIBS`, and transitive dependencies can be declared with:

```make
libDep1.cpp,DEPS = libHelper.cpp
```

## 🧪 Running Tests

To build and run all unit tests:

```bash
make tests
make run
```

To run a specific test:

```bash
make run TEST_NAME
```

## 📋 Listing and Debugging

List available test targets:

```bash
make testlist
```

See resolved dependencies for each test:

```bash
make debugdeps
```

Explain dependency graph for a specific test:

```bash
make explain TEST_NAME
```

## 📈 Dependency Graphs

Generate and view a dependency graph (requires Graphviz):

```bash
make graphdeps TEST_NAME
dot -Tpng deps.dot -o deps.png
```

Or view it directly (on Linux systems with `xdg-open`):

```bash
make viewdeps TEST_NAME
```

## 🧹 Cleaning

Clean the build output:

```bash
make clean
```

## 📎 Notes

- Ensure all paths are set relative to your `ROOT_DIR` or `BUILD_DIR` as appropriate.
- All `DEPS` and `USRLIBS` entries must reference source file names without paths.
- Graph generation requires Graphviz (`dot`) to be installed.

---

Happy testing!
