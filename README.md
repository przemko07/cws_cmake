# C WebSockets

> [!WARNING]
> The library is not in production ready state yet

Custom WebSocket implementation in C for educational and recreational purposes.


---

## Requirements

- C compiler with **C11** support
  - GCC, Clang, or MSVC
- **CMake ≥ 3.20**
- (Optional) Autobahn Test Suite for protocol testing

---

## Building with CMake

This project uses **CMake**. All targets, compiler flags, and examples are defined per-target.

| Options        |     |                          |
| ---            | --- | ---                      |
| BUILD_EXAMPLES | ON  | Build example server     |
| ENABLE_WERROR	 | OFF | Treat warnings as errors |

### 1. Configure the build
From the repository root:
```console
$ cmake -S . -B build \
  -DCMAKE_BUILD_TYPE=Release \
  -DBUILD_EXAMPLES=ON
```

### 2. Build
```console
$ cmake --build build
```

---

## Building with nob

nob is the next generation of the NoBuild idea. "nob" stands for "nobuild"

```console
$ cc -o nob nob.c
$ ./nob
$ ./build/02_plain_async_echo_server
$ firefox ./tools/send_client.html
```

---

## Run the examples

The Echo Servers in the examples are also testable with [Autobahn Test Suite](https://github.com/crossbario/autobahn-testsuite).

```console
$ ./build/02_plain_async_echo_server
$ wstest --mode fuzzingclient
```

---

## References

- https://tools.ietf.org/html/rfc6455
- https://www.websocket.org/echo.html
