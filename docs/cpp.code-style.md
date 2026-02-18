# Specification: Coding Style and Conventions for C++ Projects

## Overview

This document defines the coding style and conventions for C++ projects in
this workspace. All C++ source and header files shall follow these guidelines
to ensure consistency, readability, and maintainability.

### Language Standard

- Target **C++23** (ISO/IEC 14882:2023) as the minimum standard.
- Compile with `-std=c++23` (GCC/Clang) or `/std:c++23` (MSVC).
- Features from later standards (C++26+) may only be used when explicitly
  opted into by the project and documented as a dependency.
- Avoid compiler-specific extensions unless explicitly documented and justified.
- Use `static_assert` for compile-time checks where appropriate.

### General Principles

1. **Clarity over cleverness** — Write code that reads naturally. Favor
   explicit logic over terse template metaprogramming.
2. **Consistency** — Follow these conventions everywhere, even when personal
   preference differs.
3. **Minimize scope** — Declare variables in the narrowest scope possible.
   Prefer local variables over class members when the data is transient.
4. **RAII everywhere** — Acquire resources in constructors, release them in
   destructors. Never use raw `new`/`delete` outside of allocator code.
5. **Value semantics first** — Prefer passing and returning by value. Use
   references and pointers only when necessary (polymorphism, optional out-
   parameters, avoiding copies of large objects).
6. **No undefined behavior** — Never rely on UB. Use sanitizers
   (`-fsanitize=undefined,address`) during development.

## Naming Conventions

### General Rules

- Use **snake_case** for all identifiers — variables, functions, namespaces,
  files, and type names (classes, structs, enums, type aliases, concepts).
- Names should be descriptive and self-documenting. Avoid abbreviations unless
  they are universally understood (e.g., `len`, `ptr`, `ctx`, `buf`, `idx`).
- Single-letter names are only acceptable for loop counters (`i`, `j`, `k`),
  template parameters (`T`, `U`), and short-lived temporaries in very small
  scopes.

### Files

| Kind              | Convention              | Example                    |
|-------------------|-------------------------|----------------------------|
| Header            | `snake_case.hpp`        | `memory_pool.hpp`          |
| Source            | `snake_case.cpp`        | `memory_pool.cpp`          |
| Header-only       | `snake_case.hpp`        | `type_traits.hpp`          |
| Test source       | `test_<module>.cpp`     | `test_memory_pool.cpp`     |

Use `.hpp`/`.cpp` extensions for C++ files to distinguish them from C files.

### Variables

| Kind                  | Convention              | Example                  |
|-----------------------|-------------------------|--------------------------|
| Local variable        | `snake_case`            | `byte_count`             |
| Class data member     | `m_snake_case`          | `m_buffer_size`          |
| Static data member    | `s_snake_case`          | `s_instance_count`       |
| Global variable       | `g_snake_case`          | `g_config`               |
| Constant / `constexpr`| `k_snake_case`          | `k_max_buffer_size`      |
| Template parameter    | `T`, `Ts...`, `complex` | `T`, `As...`, `allocator`|

### Functions and Methods

| Kind                  | Convention                  | Example                  |
|-----------------------|-----------------------------|--------------------------|
| Free function         | `snake_case`                | `parse_header`           |
| Member function       | `snake_case`                | `get_size`, `reset`      |
| Static member         | `snake_case`                | `create`, `from_string`  |
| Predicate             | `is_` / `has_` / `can_`     | `is_empty`, `has_next`   |
| Getter                | `snake_case` (no `get_`)    | `size`, `name`, `data`   |
| Setter                | `set_snake_case`            | `set_size`, `set_name`   |
| Factory               | `create_` / `make_`         | `create_parser`          |

Getters that simply return a member value may omit the `get_` prefix for
brevity (e.g., `size()` rather than `get_size()`). Use `set_` for setters.

### Types

| Kind                    | Convention            | Example                     |
|-------------------------|-----------------------|-----------------------------|
| Class / struct          | `snake_case`          | `memory_pool`               |
| Enum class              | `snake_case`          | `log_level`                 |
| Enum class member       | `snake_case`          | `log_level::warning`        |
| Type alias (`using`)    | `snake_case`          | `byte_span`, `size_type`    |
| Concept (C++20)         | `snake_case`          | `hashable`, `iterable`      |
| Interface (abstract)    | `i_snake_case`        | `i_memory_bus`, `i_parser`  |

### Namespaces

Namespaces use short, lowercase `snake_case`:

```cpp
namespace mylib 
{
    namespace detail 
    {

        // internal implementation

    } // namespace detail
} // namespace mylib
```

Indent namespace bodies with 4 spaces.

Avoid deeply nested namespaces (3 levels maximum). Use `namespace::detail` or
`namespace::internal` for implementation details.

### Preprocessor Macros

Macros use `UPPER_SNAKE_CASE`, prefixed with the project name:

```cpp
#define MYLIB_VERSION_MAJOR    1
#define MYLIB_ASSERT(cond)     /* ... */
```

Minimize macro usage. Prefer `constexpr`, `inline`, templates, and `enum class`
over macros wherever possible.

## Formatting

### Indentation and Whitespace

- Use **4 spaces** per indentation level. Never use tabs.
- Maximum line length: **100 columns**. Break long lines at logical points.
- Use a single blank line to separate logical sections within a function.
- Use two blank lines to separate top-level definitions (functions, classes).
- No trailing whitespace on any line.
- Files end with exactly one newline character.
- Indent namespace bodies with 4 spaces:

```cpp
namespace mylib 
{

    class parser final
    {
        // ...
    };

} // namespace mylib
```

### Braces

Use **Allman style** (opening brace on its own line) for all blocks:

```cpp
// Functions
void process_data (std::span<const std::uint8_t> data)
{
    if (data.empty())
    {
        return;
    }

    for (auto byte : data)
    {
        handle_byte(byte);
    }
}

// Classes
class buffer final
{
public:
    buffer () = default;
    explicit buffer (std::size_t capacity);

    std::size_t size () const { return m_size; }

private:
    std::size_t m_size = 0;
};
```

Always use braces for `if`, `for`, `while`, and `do-while`, even for
single-statement bodies:

```cpp
// Correct
if (count == 0)
{
    return default_value;
}

// Wrong — never omit braces
if (count == 0)
    return default_value;
```

**Exception**: Trivial getters and setters may be written on a single line:

```cpp
std::size_t size () const { return m_size; }
void set_size (std::size_t s) { m_size = s; }
```

### Spaces

- One space after keywords: `if (`, `for (`, `while (`, `switch (`, `return `.
- One space between function/method name and parenthesis in declarations and
  definitions: `void process_data (std::span<const std::uint8_t> data);`.
- No space between function/method name and parenthesis in calls: `foo(x)`.
- One space around binary operators: `a + b`, `x = y`, `i < n`.
- No space after unary operators: `!flag`, `*ptr`, `&value`, `++i`.
- One space after commas: `foo(a, b, c)`.
- No spaces inside parentheses: `foo(a, b)`, not `foo( a, b )`.
- No spaces inside angle brackets: `std::vector<int>`, not `std::vector< int >`.

### Pointer and Reference Declarations

Avoid raw pointers where possible:

- Prefer smart pointers (`std::unique_ptr`, `std::shared_ptr`) for heap-allocated
  resource owning semantics.
- Use references (`T&`) for non-owning, non-nullable parameters.
- Use optionals (`std::optional<T>`, `std::expected<T, E>`) for nullable or 
  fallible return values.
- Use reference wrappers (`std::reference_wrapper<T>`) for nullable, non-owning
  parameters, if references are preferred for non-nullable parameters.
- Use combinations of these types as needed (e.g., 
  `std::optional<std::reference_wrapper<T>>` for an optional reference).

If raw pointers (`T*`) are necessary (e.g., for C interop, polymorphic parameters,
or optional output parameters), place the `*` next to the type, not the variable:

```cpp
void process (const T* ptr);  // pointer to const T
void set_value (T* value);    // pointer to T
```

Also, some of these types can get to be a bit verbose; try these type aliases
to improve readability:

```cpp
template <typename T>
using result = std::expected<T, std::string>;

template <typename T>
using optional_ref = std::optional<std::reference_wrapper<T>>;

template <typename T, typename E>
using expected_ref = std::expected<std::reference_wrapper<T>, E>;

template <typename T>
using result_ref = result<std::reference_wrapper<T>>;

// A helper function for `result` types:
template <typename... As>
constexpr std::unexpected<std::string> fmt_unexpected (As&&... args)
{
    return std::unexpected<std::string> 
        { std::format(std::forward<As>(args)...) };
}

// Helper to unwrap reference_wrapper-based types to T&.
// Works with: optional_ref<T>, expected_ref<T, E>, result_ref<T>,
//             and bare std::reference_wrapper<T>.

/// Unwrap std::reference_wrapper<T> to T&.
template <typename T>
constexpr T& unwrap (std::reference_wrapper<T> ref) noexcept
{
    return ref.get();
}

/// Unwrap optional_ref<T> to T&.
/// @throws std::bad_optional_access if the optional is empty.
template <typename T>
constexpr T& unwrap (optional_ref<T>& opt)
{
    return opt.value().get();
}

template <typename T>
constexpr T& unwrap (const optional_ref<T>& opt)
{
    return opt.value().get();
}

/// Unwrap expected_ref<T, E> (and result_ref<T>) to T&.
/// @throws std::bad_expected_access<E> if the expected holds an error.
template <typename T, typename E>
constexpr T& unwrap (expected_ref<T, E>& exp)
{
    return exp.value().get();
}

template <typename T, typename E>
constexpr T& unwrap (const expected_ref<T, E>& exp)
{
    return exp.value().get();
}
```

### Switch Statements

`case` labels are indented one level from `switch`. Body is indented one level
from `case`. Always include `default`:

```cpp
switch (current_state)
{
    case state::idle:
    {
        start_processing();
        break;
    }
    case state::running:
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

```cpp
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
    buffer != nullptr && 
    buffer->length > 0 && 
    buffer->length <= k_max_buffer_size
)
{
    process(buffer);
}

// Function declarations — break after name and each parameter
static processing_result process_input_buffer (
    std::span<const std::uint8_t> input,
    std::span<std::uint8_t> output, 
    std::size_t output_capacity
);

// Template declarations — break before the function signature
template <typename T, typename allocator>
void container<T, allocator>::reserve (std::size_t new_capacity)
{
    // ...
}
```

### Initializer Lists

Constructor initializer lists place each member on its own line, with the
colon and commas trailing:

```cpp
parser::parser (std::string_view source, options opts) : 
    m_source    { source },
    m_options   { std::move(opts) },
    m_position  { 0 },
    m_line      { 1 }
{
    // constructor body
}
```

## Types and Memory

### Fixed-Width Integer Types

Use `<cstdint>` for fixed-width types. If the project defines its own aliases
(e.g., `u8`, `u16`, `u32`), prefer those for consistency:

```cpp
#include <cstdint>
#include <cstddef>

std::uint8_t  byte_val;
std::uint16_t half_word;
std::uint32_t word_val;
std::size_t   element_count;
```

### Type Aliases

Use `using` instead of `typedef`:

```cpp
// Correct
using byte_span = std::span<const std::uint8_t>;
using callback = std::function<void(int)>;

// Avoid
typedef std::span<const std::uint8_t> byte_span;
```

Templates can also be aliased with `using`:

```cpp
template <typename T>
using result = std::expected<T, std::string>;
```

### Enum Classes

Always use `enum class` (scoped enums), never unscoped `enum`:

```cpp
// Correct — scoped, type-safe
enum class log_level : std::uint8_t
{
    debug   = 0,
    info    = 1,
    warning = 2,
    error   = 3,
};

// Wrong — pollutes enclosing scope
enum log_level { DEBUG, INFO, WARNING, ERROR };
```

If the enum value's underlying value needs to be referenced, use the
`std::to_underlying` helper function, and the `std::underlying_type<T>` trait to
ensure the underlying type is defined (`#include <utility>` for
`std::to_underlying` and `#include <type_traits>` for `std::underlying_type`):

```cpp
std::uint8_t level_val = std::to_underlying(log_level::warning);
```

If the enum values need to be used as bit flags, bitwise operators can be
defined for the enum class. Consider this macro for convenience:

```cpp
#define BITMASK_ENUM(E) \
    inline E operator| (E lhs, E rhs) \
    { \
        using underlying = std::underlying_type_t<E>; \
        return static_cast<E>(static_cast<underlying>(lhs) | \
            static_cast<underlying>(rhs)); \
    } \
    inline E& operator|= (E& lhs, E rhs) \
    { \
        lhs = lhs | rhs; \
        return lhs; \
    } \
    inline E operator& (E lhs, E rhs) \
    { \
        using underlying = std::underlying_type_t<E>; \
        return static_cast<E>(static_cast<underlying>(lhs) & \
            static_cast<underlying>(rhs)); \
    } \
    inline E& operator&= (E& lhs, E rhs) \
    { \
        lhs = lhs & rhs; \
        return lhs; \
    } \
    inline E operator^ (E lhs, E rhs) \
    { \
        using underlying = std::underlying_type_t<E>; \
        return static_cast<E>(static_cast<underlying>(lhs) ^ \
            static_cast<underlying>(rhs)); \
    } \
    inline E& operator^= (E& lhs, E rhs) \
    { \
        lhs = lhs ^ rhs; \
        return lhs; \
    } \
    inline E operator~ (E value) \
    { \
        using underlying = std::underlying_type_t<E>; \
        return static_cast<E>(~static_cast<underlying>(value)); \
    }
```

### Constants

Prefer `constexpr` for compile-time constants. Use `inline constexpr` for
constants in headers:

```cpp
// In a header
inline constexpr std::size_t max_buffer_size = 4096;
inline constexpr std::uint32_t magic_number  = 0xDEADBEEF;

// In a source file
constexpr int default_timeout_ms = 5000;
```

Prefer `consteval` and `constinit` for constants that require compile-time 
evaluation or initialization:

```cpp
consteval std::uint32_t make_magic_number (
    std::uint8_t a,
    std::uint8_t b,
    std::uint8_t c,
    std::uint8_t d
)
{
    return (static_cast<std::uint32_t>(a) << 24) |
           (static_cast<std::uint32_t>(b) << 16) |
           (static_cast<std::uint32_t>(c) << 8)  |
           static_cast<std::uint32_t>(d);
}
```

### Smart Pointers and Ownership

- Use `std::unique_ptr` for exclusive ownership (the default choice).
- Use `std::shared_ptr` only when ownership is genuinely shared.
- Never use raw `new`/`delete` — use `std::make_unique` and
  `std::make_shared`.
- Never use raw pointers for non-owning references — use references, optionals
  (`std::optional<std::reference_wrapper<T>>`), or smart pointers as appropriate.

```cpp
// Exclusive ownership
auto parser = std::make_unique<parser>(source);

// Shared ownership (rare — justify in comments)
auto config = std::make_shared<Config>(load_config());

// Non-owning nullable reference — optional reference wrapper
void process (const optional_ref<parser> parser);     // nullable
void process (const parser& parser);                  // non-null
```

### Containers

- Prefer `std::vector` as the default container.
- Use `std::array` for fixed-size collections known at compile time.
- Use `std::string_view` for non-owning string references (C++17).
- Use `std::span` for non-owning views of contiguous data (C++20, or a
  backport).
- Reserve capacity when the size is known or estimable:

```cpp
std::vector<token> tokens;
tokens.reserve(estimated_count);
```

### Casts

Never use C-style casts. Use the appropriate C++ cast:

| Cast                  | Use Case                                       |
|-----------------------|------------------------------------------------|
| `static_cast<T>`      | Safe, well-defined conversions                 |
| `const_cast<T>`       | Adding/removing `const` (do not use)           |
| `reinterpret_cast<T>` | Low-level bit reinterpretation (use sparingly) |
| `dynamic_cast<T>`     | Polymorphic downcasting (prefer alternatives)  |

```cpp
auto byte_val = static_cast<std::uint8_t>(int_val & 0xFF);
```

## Classes and Object-Oriented Design

### Class Layout

Organize class members in the following order. Use access specifiers as
section headers:

```cpp
class widget final
{
public:
    // --- Types and constants ---
    using size_type = std::size_t;
    static constexpr size_type default_capacity = 64;

    // --- Constructors, destructor, assignment ---
    widget ();
    explicit widget (size_type capacity);
    ~widget ();

    widget (const widget& other);
    widget& operator= (const widget& other);
    widget (widget&& other) noexcept;
    widget& operator= (widget&& other) noexcept;

    // --- Public interface ---
    size_type size () const;
    bool is_empty () const;
    void clear ();

protected:
    // --- Protected interface (for derived classes) ---
    void on_resize (size_type new_size);

private:
    // --- Private helpers (first) ---
    void grow (size_type min_capacity);

    // --- Data members (next) ---
    std::unique_ptr<std::uint8_t[]> m_data;
    size_type m_size     = 0;
    size_type m_capacity = 0;

    // -- Deleted operations (always last) ---
};
```

- All classes not intended for inheritance (or further derivation) should be 
  marked `final`.
- Group class contents in the following order:
    1. Friend classes
    2. Public types and constants
    3. Public methods
        - Constructors, then destructor first
        - Copy operations, then move operations
        - Static methods
        - Pure virtual methods (if any)
        - Non-pure virtual methods
        - Non-virtual methods
        - Getters and setters with side effects
        - Trivial getters and setters (can be one-liners)
        - Operator overloads (including conversion operators)
    4. Public members (if any, but prefer none)
    5. Protected types, methods and members (if any, same order as public)
    6. Private types, methods and members (if any, same order as public)
    7. Deleted operations (copy/move constructors and assignment operators, or
       any other operations that are explicitly disabled)

### Constructors

- Mark single-argument constructors `explicit` to prevent implicit conversions.
- Use member initializer lists, not assignment in the constructor body.
- Use in-class member initializers for default values.
- Use `= default` for trivial constructors/destructors.
- Use `= delete` to explicitly disable unwanted operations.

```cpp
class connection final
{
public:
    connection () = default;
    explicit connection (std::string_view address);

    // Movable
    connection (connection&&) noexcept = default;
    connection& operator= (connection&&) noexcept = default;

private:
    std::string m_address;
    int m_socket = -1;

private:
    // Non-copyable
    connection (const connection&) = delete;
    connection& operator= (const connection&) = delete;

};
```

### Inheritance and Polymorphism

- Use `override` on every overridden virtual method.
- Use `final` on classes or methods that should not be further overridden.
- Make destructors `virtual` in base classes intended for polymorphism.
- Prefer composition over inheritance when there is no true "is-a"
  relationship.
- Prefix abstract interface class names with `i_`:

```cpp
class i_renderer
{
public:
    virtual ~i_renderer () = default;

    virtual void begin_frame () = 0;
    virtual void end_frame () = 0;
    virtual void draw_sprite(const sprite& sprite) = 0;
};

class software_renderer final : public i_renderer
{
public:
    void begin_frame () override;
    void end_frame () override;
    void draw_sprite (const sprite& sprite) override;
};
```

### Structs vs. Classes

- Use `struct` for **passive data** with no invariants to maintain (all
  members public, no private state).
- Use `class` when the type maintains **invariants** (encapsulates state,
  has meaningful constructors/methods).

```cpp
// Struct — plain data, no invariants
struct point final
{
    float x = 0.0f;
    float y = 0.0f;
};

// Class — maintains invariants
class circle final
{
public:
    explicit circle (float radius);
    float area () const;

private:
    float m_radius;  // invariant: m_radius >= 0
};
```

### The Rule of Five (or Zero)

- If the class needs a custom destructor, copy constructor, copy assignment,
  move constructor, or move assignment — it likely needs **all five**.
- If the class does not manage resources directly (uses smart pointers, RAII
  wrappers), it needs **none** of them — the Rule of Zero.

## Functions

### Design Guidelines

- Each function should do **one thing** and do it well.
- Keep functions under **50 lines** where practical. If a function grows
  beyond this, look for opportunities to extract helpers.
- Limit functions to **5 or fewer parameters**. If more are needed, group
  related parameters into a struct or options object.
- Use `[[nodiscard]]` on functions whose return value must not be ignored
  (especially error codes, factory functions, and pure computations).

### Parameter Passing

| Type / Size                     | Convention                         |
|---------------------------------|------------------------------------|
| Primitive (`int`, `bool`, etc.) | Pass by value                      |
| Small trivial struct (≤16 bytes)| Pass by value                      |
| Large or non-trivial type       | Pass by `const&`                   |
| Output parameter                | Return by value (preferred)        |
| Optional output                 | Pointer to mutable (`T*`)          |
| Sink parameter (will be moved)  | Pass by value, then `std::move`    |
| Polymorphic / nullable          | Pointer (`const T*` or `T*`)       |

```cpp
// const-ref for large input, value return for output
std::vector<token> tokenize (const std::string& source);

// Sink parameter — passed by value, moved into member
void set_name (std::string name)
{
    m_name = std::move(name);
}

// Optional output via pointer
bool parse (const std::string& input, result* out_result = nullptr);
```

### Return Values

- Prefer returning values over output parameters.
- Use `std::optional<T>` for operations that may not produce a result.
- Use structured bindings (C++17) to unpack multiple return values from
  `std::pair` or `std::tuple`.

```cpp
// Preferred — clear intent
std::optional<config> load_config (const std::filesystem::path& path);

// Structured binding
auto [success, message] = validate_input(data);
```

### `const` Correctness

- Mark member functions `const` if they do not modify the object.
- Mark local variables `const` when they are not reassigned.
- Use `const` reference parameters for inputs that are not modified.
- Prefer `constexpr` functions when the computation can be done at compile time.

### `noexcept`

- Mark move constructors and move assignment operators `noexcept`.
- Mark swap functions `noexcept`.
- Mark functions `noexcept` when they are guaranteed not to throw and are on
  performance-critical paths (e.g., called by STL containers during
  reallocation).

## Error Handling

### Strategy

Choose one primary error handling mechanism per project and use it
consistently:

| Mechanism              | When to Use                                       |
|------------------------|---------------------------------------------------|
| Exceptions             | Application-level code; recoverable errors that   |
|                        | cross module boundaries                           |
| Return codes / enums   | Low-level libraries; performance-critical paths;   |
|                        | C interop                                         |
| `std::optional`        | Operations that may legitimately produce no result |
| `std::expected` (C++23)| Richer error info without exceptions               |
| `assert` / contracts   | Programmer errors and invariant violations         |

### Exception Guidelines (if used)

- Throw by value, catch by `const` reference.
- Derive custom exceptions from `std::runtime_error` or `std::logic_error`.
- Include descriptive messages in exceptions.
- Never throw from destructors, move operations, or `noexcept` functions.

```cpp
#include <format>

class parse_error : public std::runtime_error
{
public:
    parse_error (std::size_t line, std::size_t column, const std::string& msg) :
        std::runtime_error {
            std::format("Parse error at {}:{}: {}", line, column, msg)
        },
        m_line      { line },
        m_column    { column }
    {}

    std::size_t line () const { return m_line; }
    std::size_t column () const { return m_column; }

private:
    std::size_t m_line;
    std::size_t m_column;
};
```

### Error Code Pattern (if used)

Define a project-wide error enum class:

```cpp
enum class error_code : std::uint8_t
{
    ok = 0,
    null_pointer,
    out_of_memory,
    invalid_argument,
    buffer_overflow,
    not_found,
    io_error,
};

[[nodiscard]] std::string_view to_string (error_code code);
```

### Assertions

Use `assert()` or a project-specific assertion macro for invariants that
indicate programmer errors — conditions that should **never** occur if the
code is correct:

```cpp
void process (const buffer& buf)
{
    assert(!buf.is_empty() && "process() called with empty buffer");
    // ...
}
```

Never use assertions for runtime errors (file not found, network timeout,
user input validation).

## Header Files

### Include Guards

Use `#pragma once` as the default include guard (widely supported by all major
compilers). If portability to exotic compilers is required, use traditional
`#ifndef`/`#define`/`#endif` guards:

```cpp
#pragma once

// ... header contents ...
```

### Include Order

Order includes from most specific to most general, with blank lines between
groups:

```cpp
// 1. Corresponding header (in .cpp files only)
#include "memory_pool.hpp"

// 2. Project headers
#include "error.hpp"
#include "types.hpp"

// 3. Third-party library headers
#include <fmt/core.h>

// 4. C++ standard library headers
#include <cstdint>
#include <memory>
#include <string>
#include <vector>

// 5. C compatibility headers (if needed)
#include <cstring>
```

Within each group, sort alphabetically.

### Header Content Rules

- Headers declare the **public API**. Implementation details belong in the
  `.cpp` file or in a `detail` namespace.
- Minimize includes in headers — use forward declarations when a full
  definition is not required:

```cpp
// Forward declaration — avoids including parser.hpp
class parser;

class compiler final
{
public:
    void compile (const parser& parser);
};
```

- Never put `using namespace` in a header file.
- Never put `using` declarations for standard library types at file scope in
  headers.

## Documentation

### File Headers

Every source and header file begins with a file-level comment:

```cpp
/**
 * @file    memory_pool.hpp
 * @brief   Fixed-size block memory pool allocator.
 *
 * Provides a simple pool allocator for fixed-size objects, avoiding
 * fragmentation for high-frequency allocations.
 */
```

### Class Documentation

Document every public class:

```cpp
/**
 * @brief   A resizable buffer for raw byte data.
 *
 * buffer manages a dynamically allocated byte array with automatic
 * growth. It is move-only and not thread-safe.
 */
class buffer final
{
    // ...
};
```

### Function Documentation

All public functions and methods must have Doxygen-style documentation:

```cpp
/**
 * @brief   Parse a packet header from the byte stream.
 *
 * Reads and validates a packet header starting at the given offset.
 * The stream position is advanced past the header on success.
 *
 * @param data      Input byte span.
 * @param offset    Byte offset to start reading from.
 * @return          The parsed header, or std::nullopt if the data is
 *                  malformed or truncated.
 */
[[nodiscard]]
std::optional<packet_header> parse_header (
    std::span<const std::uint8_t> data,
    std::size_t offset
);
```

### Inline Comments

- Use `//` for single-line comments.
- Use `/* ... */` for multi-line block comments within function bodies.
- Comment **why**, not **what** — the code shows what; comments explain intent.
- Place comments on the line above the code they describe:

```cpp
// CRC-32 is required by the protocol spec (Section 4.3).
auto checksum = compute_crc32(data);

std::uint32_t flags;  // packed bitmask: see Flag enum
```

### TODO and FIXME

Use standardized tags for outstanding work:

```cpp
// TODO: Add support for IPv6 addresses.
// FIXME: This overflows when count > UINT16_MAX.
// HACK: Workaround for driver bug in firmware v2.1.
```

## Modern C++ Idioms

### `auto`

Use `auto` when the type is obvious from context or when spelling it out adds
no clarity:

```cpp
// Good — type is obvious
auto parser = std::make_unique<parser>(source);
auto it = container.begin();
auto [key, value] = *map_iter;

// Bad — type is not obvious, spell it out
auto result = compute();      // what type is result?
int result = compute();       // clearer
```

Do not use `auto` for function return types unless the function is a short
lambda or the return type is genuinely complex and local.

### Range-Based For Loops

Prefer range-based `for` over index-based iteration:

```cpp
// Preferred
for (const auto& token : tokens)
{
    process(token);
}

// Use index only when the index itself is needed
for (std::size_t i = 0; i < tokens.size(); ++i)
{
    process(i, tokens[i]);
}
```

### `std::move` and Forwarding

- Use `std::move` to transfer ownership of resources.
- Never use a moved-from object except to assign a new value or destroy it.
- Use `std::forward` in perfect-forwarding contexts (template code).

### Lambda Expressions

- Keep lambdas short (under ~10 lines). Extract named functions for longer
  logic.
- Capture by reference `[&]` for short-lived lambdas. Capture by value `[=]`
  or explicitly list captures for lambdas that outlive the current scope.
- Prefer named functions over lambdas when the lambda would be reused or is
  complex enough to benefit from a name.

```cpp
// Short, clear lambda
auto is_positive = [] (int x) { return x > 0; };
auto count = std::count_if(values.begin(), values.end(), is_positive);

// Explicit capture for clarity
auto handler = [this, &buffer] (std::size_t offset)
{
    process_chunk(buffer, offset);
};
```

## Project Organization

### Directory Structure

A typical C++ project layout:

```
project/
├── docs/               # Specifications and documentation
├── include/             # Public headers (or inline in src/)
│   └── project/
│       ├── module_a.hpp
│       └── module_b.hpp
├── src/                 # Source files and private headers
│   ├── module_a.cpp
│   └── module_b.cpp
├── tests/               # Test source files
│   ├── test_module_a.cpp
│   └── test_module_b.cpp
├── scripts/             # Build and utility scripts
├── CMakeLists.txt       # or premake5.lua, Makefile, etc.
└── README.md
```

### Compilation Warnings

Compile with strict warnings enabled. Recommended flags (GCC/Clang):

```
-Wall -Wextra -Wpedantic -Werror
-Wshadow -Wconversion -Wsign-conversion
-Wnon-virtual-dtor -Woverloaded-virtual
-Wno-unused-parameter  (only if callbacks require fixed signatures)
```

### Build Hygiene

- Every source file must compile cleanly with zero warnings.
- Enable sanitizers in debug builds: `-fsanitize=address,undefined`.
- Use `clang-tidy` or a similar static analysis tool in CI.
- Run Valgrind or AddressSanitizer for memory safety validation.
