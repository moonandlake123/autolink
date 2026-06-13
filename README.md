# autolink

C++20 `-lfoo` cannot discover module interfaces. This prototype provides a solution.

---

`autolink` is a compiler wrapper that simulates behavior intended for the compiler driver itself. It is transparent to the existing build process.

When a `.a` archive is passed to the compiler, the script automatically
scans the archive for module interface source files, extracts
and compiles them to BMI on-the-fly, then injects the BMI path into the
compiler command. The remaining build proceeds as normal.

Requires no new file format, no BMI standardization, and no changes to
existing build systems.

---

## Requirements

Python 3.13+

---

## Usage

`autolink` takes the compiler path as its first argument, followed by
the compiler's regular flags. The `.a` archive must be passed as an
explicit path.

```bash
# 1. Author: package .cppm alongside .o into .a
clang++ -std=c++20 -c hello.cppm -o hello.o
ar rcs libhello.a hello.o hello.cppm

# 2. Consumer: build with autolink
./autolink clang++ -std=c++20 main.cpp libhello.a -o main

# 3. Run
./main
```

---

## Rationale

- The `.a` format has supported arbitrary members since 1971; `ar` imposes
no restriction. The same logic applies to Windows `.lib` archives.

- BMI is compiled locally by the consumer's compiler, ensuring version
and flag compatibility.

- Archives containing only object files are passed through unchanged.

---

## Limitations

- Verified on Clang 21 under Termux. GCC and MSVC adaptations follow
the same logic.

- `.a`path must be explicit. The prototype currently uses Clang-specific flags ( --precompile ,  -fprebuilt-module-path ).

---

## WG21 / SG15

This prototype demonstrates the following proposal:

> A compiler, when resolving `-lfoo`, SHOULD inspect the corresponding
> static archive for C++ module interface source files and compile
> the required BMI automatically.

This mechanism requires no changes to BMI formats, no ABI
standardization, and no modifications to build systems.

---

Public domain