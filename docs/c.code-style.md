# Specification: Coding Style and Conventions for C Projects

## Overview

This document defines the coding style and conventions for C projects in this
workspace. All C source and header files shall follow these guidelines to
ensure consistency, readability, and maintainability.

### Language Standard

- Target **C23** (ISO/IEC 9899:2023) as the minimum standard.
- Compile with `-std=c23` (GCC/Clang) or the equivalent flag for your compiler.
- Avoid compiler-specific extensions unless explicitly documented and justified.
- Use `static_assert` (a keyword since C23; `_Static_assert` in C11/C17) for
  compile-time checks where appropriate.

### General Principles

1. **Clarity over cleverness** — Write code that reads naturally. Favor
   explicit logic over terse one-liners.
2. **Consistency** — Follow these conventions everywhere, even when personal
   preference differs.
3. **Minimize scope** — Declare variables in the narrowest scope possible.
   Avoid file-scope globals unless truly necessary.
4. **Fail early, fail loudly** — Validate inputs at function boundaries.
   Return error codes or assert on invariants.
5. **No undefined behavior** — Never rely on UB. Use sanitizers (`-fsanitize=
   undefined,address`) during development.

## Naming Conventions

### General Rules

- Use **snake_case** for all identifiers (variables, functions, types, files).
- Names should be descriptive and self-documenting. Avoid abbreviations unless
  they are universally understood (e.g., `len`, `ptr`, `ctx`, `buf`, `idx`).
- Single-letter names are only acceptable for loop counters (`i`, `j`, `k`)
  and short-lived temporaries in very small scopes.

### Files

| Kind          | Convention              | Example                    |
|---------------|-------------------------|----------------------------|
| Header        | `snake_case.h`          | `memory_pool.h`            |
| Source        | `snake_case.c`          | `memory_pool.c`            |
| Test source   | `test_<module>.c`       | `test_memory_pool.c`       |

### Variables

| Kind                  | Convention              | Example                  |
|-----------------------|-------------------------|--------------------------|
| Local variable        | `snake_case`            | `byte_count`             |
| Global variable       | `g_snake_case`          | `g_instance_count`       |
| Static file-scope     | `s_snake_case`          | `s_initialized`          |
| Constant / `const`    | `snake_case`            | `max_buffer_size`        |
| Pointer               | Suffix not required     | `node`, `buffer`         |

### Functions

| Kind                  | Convention                  | Example                  |
|-----------------------|-----------------------------|--------------------------|
| Public function       | `prefix_snake_case`         | `pool_alloc`             |
| Static/internal       | `snake_case`                | `grow_buffer`            |
| Callback / hook       | `on_snake_case`             | `on_data_received`       |
| Predicate (bool-like) | `is_` / `has_` / `can_`     | `is_empty`, `has_next`   |

All public functions in a module should share a common prefix matching the
module name (e.g., `pool_init`, `pool_alloc`, `pool_free`).

### Types

| Kind                  | Convention              | Example                  |
|-----------------------|-------------------------|--------------------------|
| `typedef` struct      | `snake_case_t`          | `hash_map_t`             |
| `typedef` enum        | `snake_case_t`          | `log_level_t`            |
| `typedef` function ptr| `snake_case_fn`         | `compare_fn`             |
| Opaque handle         | `snake_case_t`          | `context_t`              |

### Preprocessor

| Kind                  | Convention              | Example                  |
|-----------------------|-------------------------|--------------------------|
| Object-like macro     | `UPPER_SNAKE_CASE`      | `MAX_CLIENTS`            |
| Function-like macro   | `UPPER_SNAKE_CASE`      | `ARRAY_LEN(arr)`         |
| Include guard         | `PROJECT_MODULE_H`      | `MYLIB_MEMORY_POOL_H`    |
| Feature toggle        | `PROJECT_ENABLE_X`      | `MYLIB_ENABLE_LOGGING`   |

### Enum Members

Enum members use `UPPER_SNAKE_CASE`, prefixed with the enum's abbreviated name:

```c
typedef enum log_level 
{
    LOG_LEVEL_DEBUG,
    LOG_LEVEL_INFO,
    LOG_LEVEL_WARNING,
    LOG_LEVEL_ERROR,
    LOG_LEVEL_COUNT          /* sentinel — total number of levels */
} log_level_t;
```

## Formatting

### Indentation and Whitespace

- Use **4 spaces** per indentation level. Never use tabs.
- Maximum line length: **100 columns**. Break long lines at logical points.
- Use a single blank line to separate logical sections within a function.
- Use two blank lines to separate top-level definitions (functions, structs).
- No trailing whitespace on any line.
- Files end with exactly one newline character.

### Braces

Use **Allman style** (opening brace on its own line) for all blocks:

```c
// Functions
void process_data (const uint8_t* data, size_t len)
{
    if (data == NULL)
    {
        return;
    }

    for (size_t i = 0; i < len; ++i)
    {
        handle_byte(data[i]);
    }
}

// Structs
typedef struct vector3
{
    float x;
    float y;
    float z;
} vector3_t;
```

Always use braces for `if`, `for`, `while`, and `do-while`, even for
single-statement bodies:

```c
// Correct
if (count == 0)
{
    return DEFAULT_VALUE;
}

// Wrong — never omit braces
if (count == 0)
    return DEFAULT_VALUE;
```

### Spaces

- One space after keywords: `if (`, `for (`, `while (`, `switch (`, `return `.
- One space between function name and parenthesis in function declarations and
  definitions: `void process_data (const uint8_t* data, size_t len)`, not
  `void process_data(const uint8_t* data, size_t len)`.
- No space between function name and parenthesis in function calls: `foo(x)`, 
  not `foo (x)`.
- One space around binary operators: `a + b`, `x = y`, `i < n`.
- No space after unary operators: `!flag`, `*ptr`, `&value`, `++i`.
- One space after commas: `foo(a, b, c)`.
- No spaces inside parentheses: `foo(a, b)`, not `foo( a, b )`.

### Pointer and Array Declarations

Attach the `*` to the variable name, not the type:

```c
int *ptr;           // correct
int* ptr;           // wrong
int *a, *b;         // correct — both are pointers
const char *name;   // correct
```

Never leave pointers uninitialized. If you need a null pointer, initialize it
explicitly:

```c
int *ptr = NULL;    // correct
int *ptr;           // wrong — uninitialized pointer
```

### Switch Statements

`case` labels are indented one level from `switch`. Body is indented one level
from `case`. Always include `default`:

```c
switch (state)
{
    case STATE_IDLE:
    {
        start_processing();
        break;
    }
    case STATE_RUNNING:
    {
        continue_processing();
        break;
    }
    default:
    {
        handle_unknown_state(state);
        break;
    }
}
```

### Line Breaking

Try to keep lines at or around 80 characters for readability; do not exceed 100
characters. Break long lines at logical points, such as after commas in argument
lists, before binary operators, or after opening parentheses in function calls:

```c
// Function calls — break after opening paren, align arguments vertically
result = compute_transform(
    source_buffer, 
    source_length,
    destination_buffer, 
    destination_capacity,
    transform_flags
);

// Conditionals — break before logical operators, align vertically
if (
    buffer != NULL && 
    buffer->length > 0 && 
    buffer->length <= MAX_BUFFER_SIZE
)
{
    process(buffer);
}

// Function declarations — break after name and each parameter
static processing_result_t process_input_buffer (
    const uint8_t *input, 
    size_t input_len,
    uint8_t *output, 
    size_t output_capacity
);
```

## Types and Memory

### Fixed-Width Integer Types

Always use fixed-width integer types from `<stdint.h>` when a specific size is
required. Use `<stdbool.h>` for boolean values:

```c
#include <stdint.h>
#include <stdbool.h>

uint8_t  byte_val;
uint16_t half_word;
uint32_t word_val;
uint64_t dword_val;
int32_t  signed_val;
bool     is_ready;
size_t   element_count;    // for sizes and counts, not for data storage
```

If the project defines its own type aliases (e.g., `u8`, `u16`, `u32`), prefer
those over the bare `stdint.h` names for consistency within the project.

### Struct Design

- In libraries, structs should be designed to be **opaque** — the definition is
    hidden in the `.c` file, and users interact with them via pointers and API
    functions. This allows for better encapsulation and future-proofing.
- Always `typedef` structs so they can be used without the `struct` keyword.
  Name the struct and the typedef the same (e.g., `typedef struct foo { ... } foo_t;`).
- Initialize all fields — use designated initializers (C99+) where practical.
- Group related fields together. Add comments for non-obvious fields.
- Place the most frequently accessed fields first (cache-friendliness).

```c
typedef struct packet_header
{
    uint32_t magic;         /** @brief Magic number for validation. */
    uint16_t version;       /** @brief Protocol version. */
    uint16_t flags;         /** @brief Bitfield of packet flags. */
    uint32_t payload_size;  /** @brief Size of the payload in bytes. */
} packet_header_t;

// Example of designated initializer
packet_header_t header = {
    .magic        = PACKET_MAGIC,
    .version      = 1,
    .flags        = 0,
    .payload_size = 0,
};
```

### Memory Management

- Every `malloc`/`calloc`/`realloc` must have a corresponding `free`.
- Always check the return value of allocation functions.
- Set pointers to `NULL` after freeing.
- Prefer `calloc` over `malloc` when zero-initialization is desired.
- Use a consistent init/destroy pattern for structs that own resources:

```c
// Initialization — returns 0 on success, non-zero on error
int buffer_init (buffer_t *buf, size_t capacity)
{
    if (buf == NULL || capacity == 0)
    {
        return -1;
    }

    buf->data = calloc(capacity, sizeof(uint8_t));
    if (buf->data == NULL)
    {
        return -1;
    }

    buf->capacity = capacity;
    buf->length   = 0;
    return 0;
}

// Cleanup — safe to call multiple times
void buffer_destroy (buffer_t *buf)
{
    if (buf == NULL)
    {
        return;
    }

    free(buf->data);
    buf->data     = NULL;
    buf->capacity = 0;
    buf->length   = 0;
}
```

Prefer macros to ensure a uniform pattern for resource allocation:

```c
#define ALLOC(COUNT, TYPE) \
    ((TYPE *) malloc((COUNT) * sizeof(TYPE)))
#define ALLOC_ZERO(COUNT, TYPE) \
    ((TYPE *) calloc((COUNT), sizeof(TYPE)))
#define RE_ALLOC(PTR, COUNT, TYPE) \
    ((TYPE *) realloc((PTR), (COUNT) * sizeof(TYPE)))
```

Prefer a macro for safe-freeing pointers:

```c
#define SAFE_FREE(PTR) \
    do { \
        if ((PTR) != NULL) { \
            free(PTR); \
            (PTR) = NULL; \
        } \
    } while (0)
```

### Constants and Enums

- Prefer `enum` values over `#define` for related integer constants (they are
  visible in debuggers and have type information).
- Use `static const` variables for typed constants where appropriate.
- Use `#define` only for values that must be used in preprocessor directives
  or array-size declarations.

```c
// Preferred — typed, debugger-visible
typedef enum direction
{
    DIRECTION_NORTH = 0,
    DIRECTION_SOUTH,
    DIRECTION_EAST,
    DIRECTION_WEST,
    DIRECTION_COUNT
} direction_t;

// Acceptable — needed for array sizes
#define MAX_NAME_LENGTH 256
```

Prefer a consistent typedef for referencing enumerations in general, in case the
underlying type may be interchangable, opaque or otherwise abstracted:

```c
typedef uint32_t enum_t;
```

## Functions

### Design Guidelines

- Each function should do **one thing** and do it well.
- Keep functions under **50 lines** where practical. If a function grows
  beyond this, look for opportunities to extract helpers.
- Limit functions to **5 or fewer parameters**. If more are needed, group
  related parameters into a struct.
- Use `static` for all functions that are not part of the module's public API.
- Order functions in a source file: static helpers first, then public API
  functions. Alternatively, use forward declarations at the top and place
  public API functions first for readability.

### Parameter Conventions

- Input parameters come first, output parameters last.
- Use `const` on pointer parameters that are not modified:

```c
// input, input, output
int parse_header (
    const uint8_t *data, 
    size_t data_len,
    packet_header_t *out_header
);
```

- For optional output parameters, allow `NULL` and document it:

```c
/**
 * @brief Decode a packet from the buffer.
 *
 * @param data      Input buffer.
 * @param data_len  Length of the input buffer.
 * @param out_size  If non-NULL, receives the number of bytes consumed.
 * @return          Decoded packet, or NULL on error.
 */
packet_t *packet_decode (
    const uint8_t *data, 
    size_t data_len,
    size_t *out_size
);
```

### Return Values

- Functions which return a pointer should return that pointer on success, or
  `NULL` on error.
- All other functions should return an **error code**, be it a `bool`, `int`, or
  `enum`. The specific convention (e.g., `0` for success, non-zero for error) should
  be consistent across the project. Functions should use output parameters for
  actual return values, not the function's return value.
- Do not use `void` as a return type.
- Document the meaning of every possible return value.
- If a function can fail in a way which sets the `errno` variable, be sure to
  document that as well.

## Error Handling

### Strategy

- Use return codes for recoverable errors.
- Use `assert()` for programmer errors and invariant violations (conditions
  that should never occur if the code is correct).
- Never use `assert()` for runtime errors (e.g., file not found, allocation
  failure) — those must be handled gracefully.

### Error Code Pattern

Define a project-wide error enum:

```c
typedef enum error_code
{
    ERROR_OK = 0,           /** @brief Success. */
    ERROR_NULL_POINTER,     /** @brief NULL pointer passed where not allowed. */
    ERROR_OUT_OF_MEMORY,    /** @brief Memory allocation failed. */
    ERROR_INVALID_ARGUMENT, /** @brief Argument value is out of range. */
    ERROR_BUFFER_OVERFLOW,  /** @brief Operation would exceed buffer capacity. */
    ERROR_NOT_FOUND,        /** @brief Requested item was not found. */
    ERROR_IO,               /** @brief I/O operation failed. */
    ERROR_COUNT
} error_code_t;

/**
 * @brief Return a human-readable string for the given error code.
 */
const char *error_to_string (error_code_t code);
```

### Cleanup Pattern

Use `goto`-based cleanup for functions that acquire multiple resources:

```c
int process_file (const char *path)
{
    int result = ERROR_OK;

    FILE *file = fopen(path, "rb");
    if (file == NULL)
    {
        result = ERROR_IO;
        goto cleanup;
    }

    uint8_t *buffer = malloc(BUFFER_SIZE);
    if (buffer == NULL)
    {
        result = ERROR_OUT_OF_MEMORY;
        goto cleanup_file;
    }

    // ... do work with file and buffer ...

    free(buffer);
    cleanup_file:
    {
        fclose(file);
    }
    cleanup:
    {
        return result;
    }
}
```

**A note on `goto`**: While `goto` is often discouraged, it is an accepted pattern
in C for error handling and cleanup, as it avoids deep nesting and code duplication. 
Use it judiciously and always with clear labels. Also, encapsulate all `goto`
targets in a single code block `{ ... }` to limit their scope and improve readability.

## Header Files

### Include Guards

Use `#ifndef`/`#define`/`#endif` include guards. The guard name follows the
pattern `PROJECT_MODULE_H`:

```c
#ifndef MYLIB_MEMORY_POOL_H
#define MYLIB_MEMORY_POOL_H

// ... header content ...

#endif // MYLIB_MEMORY_POOL_H
```

`#pragma once` is acceptable as a project-wide convention if all target
compilers support it, but include guards are the portable default.

### Include Order

Order includes from most specific to most general, with blank lines between
groups:

```c
// 1. Corresponding header (in .c files only)
#include "memory_pool.h"

// 2. Project headers
#include "error.h"
#include "types.h"

// 3. Third-party library headers
#include <libfoo/foo.h>

// 4. Standard library headers
#include <stdint.h>
#include <stdlib.h>
#include <string.h>
```

Within each group, sort alphabetically.

### Header Content Rules

- Headers declare the **public API** only. Implementation details belong in
  the `.c` file.
- Never define non-`inline` functions or non-`static` variables in headers.
- Use forward declarations to minimize header dependencies where possible.
- Wrap the header body in `extern "C"` guards for C++ compatibility:

```c
#ifndef MYLIB_PARSER_H
#define MYLIB_PARSER_H

#ifdef __cplusplus
extern "C" {
#endif

// Public API declarations go here

#ifdef __cplusplus
}
#endif

#endif // MYLIB_PARSER_H
```

## Documentation

### File Headers

Every source and header file begins with a file-level comment:

```c
/**
 * @file    memory_pool.h
 * @brief   Fixed-size block memory pool allocator.
 *
 * Provides a simple pool allocator for fixed-size objects, avoiding
 * fragmentation for high-frequency allocations.
 */
```

### Function Documentation

All public functions must have Doxygen-style documentation:

```c
/**
 * @brief   Allocate a block from the pool.
 *
 * Returns a pointer to an uninitialized block of memory from the pool.
 * The caller is responsible for returning the block via pool_free().
 *
 * @param pool  Pointer to the memory pool.
 * @return      Pointer to the allocated block, or NULL if the pool is
 *              exhausted.
 */
void *pool_alloc (memory_pool_t *pool);
```

### Inline Comments

- Use `/* ... */` block comments for multi-line explanations.
- Use `//` for short, single-line comments.
    - The minimum standard is C17, so `//` comments are allowed and preferred
      for single-line comments.
- Comment **why**, not **what** — the code shows what; comments explain intent.
- Place comments on the line above the code they describe, not inline unless
  very short:

```c
// Calculate the checksum using CRC-32, as required by the protocol spec.
uint32_t checksum = crc32(data, data_len);

uint32_t flags;     // packed bitmask: see FLAG_* constants
```

### TODO and FIXME

Use standardized tags for outstanding work:

```c
// TODO: Add support for IPv6 addresses.
// FIXME: This overflows when count > UINT16_MAX.
// HACK: Workaround for driver bug in firmware v2.1.
```

## Preprocessor

### Macro Safety

- Wrap macro arguments in parentheses.
- Wrap the entire macro body in parentheses (for expression macros) or
  `do { ... } while (0)` (for statement macros).

```c
// Expression macro — parenthesized
#define MIN(a, b)   ((a) < (b) ? (a) : (b))

// Statement macro — do/while(0)
#define LOG_ERROR(fmt, ...)                         \
    do {                                            \
        fprintf(stderr, "ERROR: " fmt "\n",         \
                ##__VA_ARGS__);                     \
    } while (0)
```

### Conditional Compilation

- Keep `#ifdef` / `#if` blocks short and self-contained.
- Prefer runtime checks over compile-time `#ifdef` when the performance cost
  is negligible.
- Comment the `#endif` with the condition it closes:

```c
#ifdef MYLIB_ENABLE_LOGGING
    log_message(LOG_LEVEL_DEBUG, "Initialized with %zu items", count);
#endif // MYLIB_ENABLE_LOGGING
```

## Project Organization

### Directory Structure

A typical C project layout:

```
project/
├── include/             # Public headers (or inline in src/)
│   └── project/
│       ├── module_a.h
│       └── module_b.h
├── src/                 # Source files and private headers
│   ├── module_a.c
│   └── module_b.c
├── tests/               # Test source files (for unit tests, integration tests, etc.)
│   ├── test_module_a.c
│   └── test_module_b.c
└── CMakeLists.txt       # or premake5.lua, Makefile, etc. for project-level builds
```

**Note**: The parent folder of the above structure is in the `projects/` directory
of the workspace's root folder structure.

### Compilation Warnings

Compile with strict warnings enabled. Recommended flags (GCC/Clang):

```
-Wall -Wextra -Wpedantic -Werror
-Wshadow -Wconversion -Wsign-conversion
-Wstrict-prototypes -Wmissing-prototypes
-Wno-unused-parameter  (only if callbacks require fixed signatures)
```

### Build Hygiene

- Every source file must compile cleanly with zero warnings.
- Enable sanitizers in debug builds: `-fsanitize=address,undefined`.
- Run `valgrind` or equivalent for memory leak detection in CI.
