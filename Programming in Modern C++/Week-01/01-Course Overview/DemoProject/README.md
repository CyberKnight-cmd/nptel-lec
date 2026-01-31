# 📘 Modern CMake – Practical Guide (Beginner → Industry)

> This document explains **how to write CMakeLists.txt** for modern C++ projects:
>
> * initialize a project
> * build & run
> * multi-file layout
> * headers & include directories
> * Debug vs Release
> * best practices

Target setup:

* **Compiler**: MSVC (Windows)
* **Generator**: Ninja
* **IDE**: VS Code
* **Build system**: CMake (modern style)

---

## 1️⃣ What CMake actually is (mental model)

CMake is **not a compiler**.

CMake:

* reads **CMakeLists.txt**
* figures out *how to build*
* generates files for:

  * Ninja
  * Visual Studio
  * Make

Then:

```
CMake → Ninja → MSVC → .exe
```

---

## 2️⃣ Minimal CMakeLists.txt (hello world)

This is the **smallest valid modern CMake project**.

```cmake
cmake_minimum_required(VERSION 3.20)

project(hello_cpp
    VERSION 1.0
    LANGUAGES CXX
)

add_executable(hello_cpp
    src/main.cpp
)
```

### What each line means

```cmake
cmake_minimum_required(VERSION 3.20)
```

✔ Minimum CMake version
✔ Prevents weird legacy behavior

---

```cmake
project(hello_cpp VERSION 1.0 LANGUAGES CXX)
```

✔ Project name
✔ Enables C++
✔ Sets metadata

---

```cmake
add_executable(hello_cpp src/main.cpp)
```

✔ Creates an executable target
✔ Name becomes `hello_cpp.exe` on Windows

---

## 3️⃣ Enforcing C++ standard (always do this)

```cmake
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

Meaning:

* Use C++20
* Fail if compiler can’t support it

Never rely on compiler defaults.

---

## 4️⃣ Recommended project structure (industry standard)

```
my_project/
├── CMakeLists.txt
├── include/
│   └── app.hpp
├── src/
│   ├── main.cpp
│   └── app.cpp
├── build/          # generated (never commit)
└── README.md
```

---

## 5️⃣ Multi-file project (THIS IS IMPORTANT)

### `include/app.hpp`

```cpp
#pragma once

void say_hello();
```

---

### `src/app.cpp`

```cpp
#include <iostream>
#include "app.hpp"

void say_hello() {
    std::cout << "Hello from app.cpp\n";
}
```

---

### `src/main.cpp`

```cpp
#include "app.hpp"

int main() {
    say_hello();
    return 0;
}
```

---

## 6️⃣ CMakeLists.txt for multi-file project

```cmake
cmake_minimum_required(VERSION 3.20)

project(my_project LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(my_project
    src/main.cpp
    src/app.cpp
)

target_include_directories(my_project
    PRIVATE include
)
```

---

### Why `target_include_directories`?

This tells the compiler:

```
#include "app.hpp" → look inside ./include
```

❌ Do NOT use global include directories
✅ Always attach includes to a target

---

## 7️⃣ Target-based CMake (core modern rule)

Modern CMake rule:

> **Everything belongs to a target**

Bad (old CMake):

```cmake
include_directories(include)
add_definitions(-DFOO)
```

Good (modern CMake):

```cmake
target_include_directories(my_project PRIVATE include)
target_compile_definitions(my_project PRIVATE FOO)
```

This makes:

* builds predictable
* dependencies clean
* projects scalable

---

## 8️⃣ Build types: Debug vs Release

CMake supports build types:

| Type           | Use                   |
| -------------- | --------------------- |
| Debug          | Debugging             |
| Release        | Performance           |
| RelWithDebInfo | Debug + optimizations |

### With Ninja (single-config)

```bat
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug
cmake --build build
```

For release:

```bat
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build
```

VS Code handles this automatically via CMake Tools.

---

## 9️⃣ Common CMake commands (you should memorize these)

```bat
cmake -S . -B build          # configure
cmake --build build          # build
cmake --build build --clean-first
```

Delete build folder anytime → safe.

---

## 🔟 What NOT to do (very important)

❌ Don’t put build files in source directory
❌ Don’t commit `build/`
❌ Don’t use `file(GLOB ...)` for sources (advanced topic)
❌ Don’t use global flags (`CMAKE_CXX_FLAGS`)

---

## 1️⃣1️⃣ Adding compiler warnings (recommended)

```cmake
if (MSVC)
    target_compile_options(my_project PRIVATE /W4)
else()
    target_compile_options(my_project PRIVATE -Wall -Wextra -Wpedantic)
endif()
```

This is **cross-platform safe**.

---

## 1️⃣2️⃣ Multiple executables (advanced but common)

```cmake
add_executable(tool src/tool.cpp)
add_executable(app  src/main.cpp src/app.cpp)

target_include_directories(app PRIVATE include)
```

Each executable is its own **target**.

---

## 1️⃣3️⃣ Libraries (preview)

Static library:

```cmake
add_library(mylib
    src/lib.cpp
)

target_include_directories(mylib PUBLIC include)
```

Link it:

```cmake
target_link_libraries(my_project PRIVATE mylib)
```

(We’ll go deep into this later.)

---

## 1️⃣4️⃣ `.gitignore` (do this immediately)

```
build/
.vscode/
```

---

## 1️⃣5️⃣ Mental checklist when starting a new C++ project

✔ Create folder
✔ Write `CMakeLists.txt`
✔ Add `src/` and `include/`
✔ Configure with CMake
✔ Build with Ninja
✔ Run executable

If something breaks:
👉 Delete `build/` and reconfigure

---



# 📘 Modern CMake – Part II (Libraries, Linking, Builds, Testing, Cross-Platform)

This section covers **real-world CMake usage**:

* Libraries
* Linking rules
* Debug vs Release flags
* External dependencies
* Unit testing
* Linux/macOS portability

Everything here is **industry-grade Modern CMake**.

---

## 1️⃣ Libraries & `add_library()`

### Why libraries exist

Libraries let you:

* reuse code
* separate concerns
* scale projects
* test independently

In industry, **almost everything is a library** — executables are usually thin.

---

## 📁 Project structure with a library

```
my_project/
├── CMakeLists.txt
├── include/
│   └── math/
│       └── add.hpp
├── src/
│   ├── main.cpp
│   └── math/
│       └── add.cpp
└── build/
```

---

## 📄 `include/math/add.hpp`

```cpp
#pragma once

int add(int a, int b);
```

---

## 📄 `src/math/add.cpp`

```cpp
#include "math/add.hpp"

int add(int a, int b) {
    return a + b;
}
```

---

## 📄 `src/main.cpp`

```cpp
#include <iostream>
#include "math/add.hpp"

int main() {
    std::cout << add(2, 3) << "\n";
    return 0;
}
```

---

## 📄 `CMakeLists.txt` (with a library)

```cmake
cmake_minimum_required(VERSION 3.20)
project(my_project LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 1. Create a library
add_library(mathlib
    src/math/add.cpp
)

# 2. Specify headers for users of the library
target_include_directories(mathlib
    PUBLIC include
)

# 3. Create executable
add_executable(my_project
    src/main.cpp
)

# 4. Link library to executable
target_link_libraries(my_project
    PRIVATE mathlib
)
```

---

## 2️⃣ `target_link_libraries()` — DEEPLY explained 🔥

This is **the most important CMake concept**.

### Syntax

```cmake
target_link_libraries(target
    PUBLIC  libA
    PRIVATE libB
    INTERFACE libC
)
```

---

### Meaning of keywords (THIS IS CRITICAL)

| Keyword     | Meaning                               |
| ----------- | ------------------------------------- |
| `PRIVATE`   | Used only by this target              |
| `PUBLIC`    | Used by this target **and** its users |
| `INTERFACE` | Used only by users                    |

---

### Example (mental model)

```
app  →  mathlib  →  corelib
```

If `mathlib` exposes headers from `corelib`, then:

```cmake
target_link_libraries(mathlib PUBLIC corelib)
```

Now:

* `app` automatically sees `corelib`
* include paths propagate correctly

🚨 **Never manually forward include directories** — linking handles it.

---

## 3️⃣ Debug vs Release flags (Modern way)

### Build types recap

| Type           | Purpose        |
| -------------- | -------------- |
| Debug          | Debugging      |
| Release        | Performance    |
| RelWithDebInfo | Best of both   |
| MinSizeRel     | Size optimized |

---

### NEVER do this ❌

```cmake
set(CMAKE_CXX_FLAGS "-O2")
```

This is **old, global, broken**.

---

### Correct modern way ✅

```cmake
target_compile_options(my_project PRIVATE
    $<$<CONFIG:Debug>:/Od>
    $<$<CONFIG:Release>:/O2>
)
```

This uses **generator expressions**:

* Debug → no optimization
* Release → optimized

---

### Cross-platform version

```cmake
if (MSVC)
    target_compile_options(my_project PRIVATE
        $<$<CONFIG:Debug>:/Od /Zi>
        $<$<CONFIG:Release>:/O2>
    )
else()
    target_compile_options(my_project PRIVATE
        $<$<CONFIG:Debug>:-O0 -g>
        $<$<CONFIG:Release>:-O3>
    )
endif()
```

---

## 4️⃣ External dependencies (fmt, spdlog)

### Industry rule

> **Never vendor libraries manually if CMake supports them**

---

## 🧰 Option A: FetchContent (most common)

### Example: `fmt`

```cmake
include(FetchContent)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt.git
    GIT_TAG 10.2.1
)

FetchContent_MakeAvailable(fmt)
```

Link it:

```cmake
target_link_libraries(my_project PRIVATE fmt::fmt)
```

Usage in code:

```cpp
#include <fmt/core.h>

fmt::print("Hello {}\n", 42);
```

---

### spdlog (same idea)

```cmake
FetchContent_Declare(
    spdlog
    GIT_REPOSITORY https://github.com/gabime/spdlog.git
    GIT_TAG v1.13.0
)

FetchContent_MakeAvailable(spdlog)

target_link_libraries(my_project PRIVATE spdlog::spdlog)
```

---

## 5️⃣ Unit testing with CTest

### Why CTest

* Built into CMake
* Works everywhere
* CI-friendly

---

### Project structure

```
tests/
├── test_add.cpp
```

---

### `tests/test_add.cpp`

```cpp
#include <cassert>
#include "math/add.hpp"

int main() {
    assert(add(2, 3) == 5);
    return 0;
}
```

---

### CMakeLists.txt (add this)

```cmake
enable_testing()

add_executable(test_add
    tests/test_add.cpp
)

target_link_libraries(test_add PRIVATE mathlib)

add_test(
    NAME add_test
    COMMAND test_add
)
```

Run tests:

```bat
ctest --test-dir build
```

---

## 6️⃣ CMake for Linux / macOS (portability rules)

### What changes?

Almost **nothing**.

Same `CMakeLists.txt` works on:

* Windows (MSVC)
* Linux (GCC)
* macOS (Clang)

---

### Linux build

```bash
cmake -S . -B build -G Ninja
cmake --build build
```

---

### Cross-platform rules to follow

✔ Never hardcode paths
✔ Use `target_*` commands only
✔ Avoid compiler-specific flags without `if(MSVC)`
✔ Prefer CMake packages (`fmt::fmt`)

---

## 🧠 Mental model (final form)

```
Executable
  ↓
Libraries
  ↓
External dependencies
  ↓
Compiler (MSVC/GCC/Clang)
```

CMake manages **relationships**, not files.

---

## 🔥 Absolute golden rules (pin these)

1. **Targets are everything**
2. **No global state**
3. **Libraries first, executables last**
4. **Link dependencies, don’t forward paths**
5. **Delete build/ when confused**

---

## 🚀 Where you are now

You now understand:
✔ add_library
✔ target_link_libraries
✔ Debug vs Release
✔ External deps
✔ Unit testing
✔ Cross-platform builds

This is **real CMake knowledge**, not tutorial fluff.

---


# 1️⃣ What is **fmt** and what do people mean by “fmt logging”?

### First: **fmt is NOT a logger**

`fmt` is a **formatting library**, not a logging framework.

Think of it as:

> **`printf` done right, but type-safe, fast, and modern C++**

---

## 🧠 What problem does fmt solve?

Old ways:

```cpp
printf("x = %d, y = %f\n", x, y);   // unsafe
std::cout << "x = " << x << " y = " << y << "\n"; // verbose
```

With **fmt**:

```cpp
fmt::print("x = {}, y = {}\n", x, y);
```

✔ Clean
✔ Type-safe
✔ Fast
✔ Readable

That’s why **fmt is everywhere**.

---

## So why do people say “fmt logging”?

Because **almost all modern logging libraries are built on fmt**.

Example:

* `spdlog` → uses fmt internally
* `glog` (modernized forks) → fmt-style formatting
* Game engines, servers → fmt-style logging

So when someone says:

> “We use fmt-style logging”

They mean:

```cpp
log_info("User {} logged in from {}", user_id, ip);
```

Not:

```cpp
std::cout << ...
```

---

## Minimal fmt example (no logging yet)

```cpp
#include <fmt/core.h>

int main() {
    int score = 42;
    fmt::print("Score = {}\n", score);
}
```

That’s it.
No streams. No `%d`. No pain.

---

## How fmt becomes *logging*

When combined with a logger (like spdlog):

```cpp
spdlog::info("User {} logged in", user_id);
spdlog::error("File {} not found", filename);
```

This is what people casually call **“fmt logging”**.

📌 **Key idea**:

> fmt = formatting engine
> spdlog = logging framework (built on fmt)

We start with fmt because:

* it teaches **modern CMake dependencies**
* it’s simpler
* it’s foundational

---

# 2️⃣ Generator Expressions (deep dive, but intuitive)

Generator expressions look scary, but they solve **one simple problem**:

> “Do different things depending on build configuration or platform”

They are evaluated **during build system generation**, not at runtime.

---

## The syntax (don’t panic)

```cmake
$<...>
```

That’s it. Everything scary lives inside `<>`.

---

## Example you already saw (but now explained)

```cmake
target_compile_options(my_project PRIVATE
    $<$<CONFIG:Debug>:/Od>
    $<$<CONFIG:Release>:/O2>
)
```

### Read it like English:

* `$<CONFIG:Debug>` → “If build type is Debug”
* `$<$<CONFIG:Debug>:/Od>` → “Then use `/Od`”

So this means:

* Debug → no optimization
* Release → optimized

---

## Why not use `if(Debug)`?

Because **CMake can generate multiple configurations**:

* Visual Studio
* Multi-config builds
* Presets

Generator expressions are **target-aware and config-aware**.

They are:
✔ Correct
✔ Scalable
✔ Cross-platform

---

## Common generator expressions (you WILL use these)

### Build config

```cmake
$<CONFIG:Debug>
$<CONFIG:Release>
```

### Platform

```cmake
$<PLATFORM_ID:Windows>
$<PLATFORM_ID:Linux>
```

### Compiler

```cmake
$<CXX_COMPILER_ID:MSVC>
$<CXX_COMPILER_ID:GNU>
```

---

## Example: platform-specific flags

```cmake
target_compile_definitions(my_project PRIVATE
    $<$<PLATFORM_ID:Windows>:WINDOWS_BUILD>
    $<$<PLATFORM_ID:Linux>:LINUX_BUILD>
)
```

Now your code can do:

```cpp
#ifdef WINDOWS_BUILD
// Windows-only code
#endif
```

---

## Mental model for generator expressions

> **if-statements for the build system**

Not runtime.
Not C++.
Build-time logic.

---

# 3️⃣ CMake Presets (`CMakePresets.json`) — WHY this exists

Presets solve a very real pain:

> “Everyone builds the project differently and it breaks”

---

## The old way (painful)

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug
```

Someone else:

```bash
cmake -S . -B out -G Ninja -DCMAKE_BUILD_TYPE=Release
```

CI:

```bash
cmake -S . -B build -G "Unix Makefiles"
```

Chaos 😵‍💫

---

## Presets fix this

Presets are **named, versioned build recipes**.

One file:

```
CMakePresets.json
```

Everyone uses the same commands.

---

## Minimal `CMakePresets.json`

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "debug",
      "generator": "Ninja",
      "binaryDir": "build/debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug"
      }
    },
    {
      "name": "release",
      "generator": "Ninja",
      "binaryDir": "build/release",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
  ]
}
```

---

## How you use it

```bat
cmake --preset debug
cmake --build --preset debug
```

or:

```bat
cmake --preset release
cmake --build --preset release
```

✔ No flags
✔ No guessing
✔ Works in CI
✔ VS Code understands this automatically

---

## Why industry LOVES presets

* Reproducible builds
* CI consistency
* Zero “works on my machine”
* VS Code auto-detects them
* Cross-platform by default

Most modern CMake repos **expect presets**.

---

# 🧠 How these three ideas connect

| Concept               | Purpose                                   |
| --------------------- | ----------------------------------------- |
| fmt                   | Modern formatting (foundation of logging) |
| Generator expressions | Conditional build logic                   |
| Presets               | Reproducible, shared build configs        |

Together, they give:

* clean code
* clean builds
* clean teams

---

# 🎯 What I will be learning next

Now that concepts are clear, the **best next step** is practical:

👉 **Add `fmt` to my current project properly**

* using `FetchContent`
* linked as a target
* no global state
* works Debug/Release

After that:

* add spdlog
* add presets
* add tests


