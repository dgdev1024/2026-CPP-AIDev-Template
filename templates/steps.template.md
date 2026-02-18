# Steps: Assisted Development of <PROJECT_NAME>

Below is a list of top-level steps involved in the AI-assisted development of
<PROJECT_NAME>. Each step may involve multiple sub-steps and iterations, and
will touch on various aspects of the development process including design,
coding, testing, and documentation.

## Project Overview

<!--
  Provide a high-level summary of the project: what it is, what it does, and
  the key deliverables. This grounds the AI in the project's purpose.
-->

<PROJECT_NAME> is a C/C++ <library/application/tool> that provides:
1. **<Deliverable 1>** - Brief description
2. **<Deliverable 2>** - Brief description
3. **<Deliverable 3>** - Brief description

### Architecture Overview

<!--
  Summarize the technical architecture of the project. Reference the primary
  specification document(s). Use a table to list key architectural details.
-->

| Feature               | Value                                            |
|-----------------------|--------------------------------------------------|
| Language Standard     | C17 / C++17 / C++20 / etc.                       |
| Target Platform(s)    | Linux / Windows / macOS / Cross-platform          |
| Build System          | CMake / Premake / Makefile / Meson / etc.         |
| <Feature 1>           | <Value>                                          |
| <Feature 2>           | <Value>                                          |
| <Feature 3>           | <Value>                                          |

### Implementation Pipeline

<!--
  Break the project into ordered implementation phases. Each phase groups
  related components and establishes a dependency chain. Later phases may
  depend on earlier ones.
-->

The project is developed through distinct phases:

| Phase | Component           | Description                                    |
|-------|---------------------|------------------------------------------------|
| 1     | <Component Name>    | <What this phase delivers>                     |
| 2     | <Component Name>    | <What this phase delivers>                     |
| 3     | <Component Name>    | <What this phase delivers>                     |
| ...   | ...                 | ...                                            |

### Current Implementation Status

<!--
  Track the status of each component. Update this table as steps are
  completed. Use the following status indicators:
    🔴 Not Started  —  Work has not begun
    🟡 In Progress  —  Partially implemented
    🟢 Complete     —  Fully implemented and tested
-->

| Component             | Status       | Files                                 |
|-----------------------|--------------|---------------------------------------|
| <Component 1>         | 🔴 Not Started | `<path/to/file>`                   |
| <Component 2>         | 🔴 Not Started | `<path/to/file>`                   |
| ...                   | ...          | ...                                   |

## Instructions

<!--
  These instructions tell the AI what to do each time it receives a prompt
  with this file attached. The workflow is divided into phases (A-E) that
  proceed sequentially. If prompted to iterate, the AI repeats from Phase B.
-->

Upon receiving a prompt with this file attached as context, the AI shall follow
these instructions in order. Each instruction builds on the previous, and the
AI should complete each instruction fully before moving to the next.

If prompted to repeat this process any given number of times, the AI shall
repeat the entire sequence, starting from **Phase B: Step Planning**.

### Phase A: Context Gathering

1. **Review Specifications**: Read and understand the relevant specification
   documents from the **References** section, focusing on:
   - The project's primary specification document(s)
   - The code style guide for coding conventions
   - Any supplementary specifications relevant to the current phase

2. **Assess Current State**: Review the current implementation in the project's
   source directory to understand what has been built and what remains. Check
   the **Current Implementation Status** table above and update it if needed.

3. **Review Previous Steps**: Read through completed step documents in the
   step documents directory (see **References > Development Artifacts**) to
   understand the development history and avoid redundant work.

### Phase B: Step Planning

4. **Identify the Next Step**: Based on the implementation pipeline, current
   implementation status, and previous steps, determine the next logical
   development step. Consider:
   - Dependencies between components (e.g., later phases depend on earlier ones)
   - Logical grouping of related functionality
   - Testability of the resulting implementation
   - Unless explicitly provided in the prompt, derive the step from context.

5. **Create the Step Document**: Create a new Markdown file in the step
   documents directory using the naming convention `P.S-descriptive-name.md`,
   where `P` is the phase number and `S` is the step number within that phase
   (e.g., `1.1-foundation-types.md`). Use the **Step Document Template**
   defined later in this file.

6. **Update This Document**: Add an entry to the **Steps** section below using
   the template provided in **Steps Entry Template**.

### Phase C: Implementation

7. **Implement the Step**: Write the code according to:
   - The step document's implementation plan
   - The project's code style guide (see **References**)
   - Best practices for modern C/C++ (per the project's language standard)
   
   Implementation guidelines:
   - Work incrementally, one file at a time
   - Keep functions focused and under ~50 lines where practical
   - Add comprehensive error handling with meaningful messages
   - Add documentation comments (Doxygen-style) for all public APIs
   - Ensure all behavior matches the specification document(s)

8. **Build and Fix Errors**: After each significant change:
   - Run the project's build command (see **References > Build System**)
   - Fix any compilation errors before proceeding
   - Ensure no new warnings are introduced

### Phase D: Testing and Validation

9. **Create Test Cases**: For each requirement in the step document:
   - Create C/C++ test files in the test directory using the project's test
     framework (see **Test File Template** below)
   - Write unit tests and, if applicable, integration tests that validate the
     requirement against the specification and the step's implementation, and
     integration with previous steps and functionality
   - Cover positive cases (correct behavior), negative cases (incorrect behavior),
     edge cases and "code screaming" cases (e.g., invalid inputs, resource limits,
     extreme cases, undefined behavior) to ensure robustness and security
   - Verify boundary conditions and error paths

10. **Run Tests**: Execute the test suite:
    - Run the project's test command (see **References > Build System**)
    - Verify actual output matches expected behavior per specification
    - Fix any failures before proceeding
    - Utilize memory tools like `valgrind` or sanitizers to check for leaks,
      memory errors, and undefined behavior. Fix these issues, too, before
      proceeding.

11. **Regression Testing**: Run all existing tests from previous steps to
    ensure no regressions were introduced. Also, use memory tools to check for
    regressions in memory safety and security. Fix any issues found before 
    proceeding.

### Phase E: Documentation and Completion

12. **Update Documentation**: If the step introduces new features or changes
    existing behavior:
    - Update relevant specification documents if behavior was clarified
    - Add or update code comments as needed
    - Update the **Current Implementation Status** table in this file

13. **Individual Documentation and Reference**: Create and/or update a 
    Markdown file in the step documents directory that documents the new 
    functionality in a user-friendly way. This should include:
    - An overview of the new feature or change
    - An API reference section with function signatures, parameters, return values
      and error conditions
    - Usage examples if applicable
    - Any relevant notes or caveats

14. **Review and Finalize**:
    - Verify all acceptance criteria from the step document are met
    - Ensure code follows the style guide
    - Confirm all tests pass
    - Mark the step as complete in the **Steps** section

15. **Prepare for Next Step**: Update the step document with:
    - A summary of what was accomplished
    - Any deviations from the original plan and why
    - Refined description of the next logical step

16. **Begin Next Step**: If so prompted, return to **Phase B: Step Planning**
    to identify, plan, and implement the next step.

## Test File Template

<!--
  Adapt this template to the project's test framework (e.g., Catch2, Google
  Test, doctest, Unity, or a custom framework). Update the #include, macros,
  and assertion style to match.
-->

When creating new tests for <PROJECT_NAME>, use the following template.
Place test files in the test directory organized by component:

```cpp
//
// Test: <Category>/<Test Name>
// File: tests/<category>_<test-name>.cpp
// Step: P.S - <Step Name>
//
// Description:
//   <Brief description of what this test validates>
//
// Requirements Tested:
//   - REQ-P.S-MM: <description>
//

#include "framework.hpp"          // Test framework header
#include <project/component.hpp>  // Include tested component

// === Test Cases ===

TEST_CASE("<Category>: <Test Name>", "[<tag>]") {
    // Setup
    // ...

    // Execute
    // ...

    // Verify
    REQUIRE(/* condition */);
}

// === Expected Results ===
//
// Test should verify:
//   - <expected behavior 1>
//   - <expected behavior 2>
//
```

## Steps Entry Template

<!--
  Use this format when adding entries to the Steps section at the bottom of
  this file. Each completed step gets an entry here for quick reference.
-->

When adding entries to the **Steps** section, use this format:

```markdown
### Step P.S: Descriptive Name

| Attribute       | Value                                                    |
|-----------------|----------------------------------------------------------|
| **Step File**   | [P.S-descriptive-name.md](<steps-dir>/P.S-desc-name.md)  |
| **Status**      | 🔴 Not Started / 🟡 In Progress / 🟢 Complete            |
| **Purpose**     | Brief one-line summary of the step's purpose             |
| **Components**  | Comma-separated list of affected components              |

**Description**: 
Detailed 2-3 sentence description of what this step accomplishes and why it's
necessary at this point in the development process.

**Files Affected**:
- `<path/to/file.hpp>` - Description of changes
- `<path/to/file.cpp>` - Description of changes

**Tests Added**:
- `tests/<component>_<test>.cpp` - What it tests
```

## Step Document Template

<!--
  Use this template when creating individual step documents in the step
  documents directory. Each step gets its own file following this structure.
-->

Use this template when creating step documents in the step documents directory:

```markdown
# Step P.S: Descriptive Name

| Attribute       | Value                                      |
|-----------------|--------------------------------------------|
| **Date**        | YYYY-MM-DD                                 |
| **Status**      | Not Started / In Progress / Complete       |
| **Components**  | Affected project components                |
| **Spec Refs**   | Relevant specification sections            |

## Objective

Clear, concise statement of what this step accomplishes. Should be achievable
in a single development session.

## Background

### Relevant Specifications

Quote or summarize relevant sections from the specification documents:

> From <spec-document>, Section X.Y:
> "Quoted specification text..."

### Current State

Description of the current implementation state relevant to this step.

## Requirements

Numbered, testable requirements that must be satisfied:

1. **REQ-P.S-001**: <Specific, testable requirement>
2. **REQ-P.S-002**: <Specific, testable requirement>
3. ...

## Design

### Data Structures

Description of new or modified data structures with code examples.

### Algorithms

Description of algorithms used, with pseudocode if helpful.

### Error Handling

How errors are detected, reported, and recovered from.

## Implementation Plan

Ordered list of implementation tasks:

1. [ ] Create/modify `<file.hpp>` - Add declarations for...
2. [ ] Create/modify `<file.cpp>` - Implement...
3. [ ] Update integration points - Connect...
4. [ ] Build and verify compilation

## Test Plan

| Test ID   | Description              | Setup                | Expected Result        |
|-----------|--------------------------|----------------------|------------------------|
| T-P.S-001 | <Test description>       | <Setup details>      | <Expected outcome>     |
| T-P.S-002 | <Test description>       | <Setup details>      | <Expected outcome>     |

## Acceptance Criteria

All boxes must be checked for the step to be considered complete:

- [ ] All requirements implemented
- [ ] All tests passing
- [ ] No regressions in existing tests
- [ ] Code follows project style guide
- [ ] Documentation updated
- [ ] Step document updated with completion notes

## Implementation Notes

(Filled in during/after implementation)

### Deviations from Plan

Document any deviations from the original plan and rationale.

### Lessons Learned

Notes for future reference.

## Next Step Preview

Brief description of the logical next step, referencing the implementation
pipeline and what new functionality will be needed.
```

## References

<!--
  Populate these tables with the actual paths and descriptions for the
  project. The AI uses these to locate specs, source code, build scripts,
  and development artifacts.
-->

### Internet Resources

| Resource          | URL                                 | Description                          |
|-------------------|-------------------------------------|--------------------------------------|
| Website           | `https://some.website.com`          | Some relevant website or documentation |
| API Reference     | `https://api.reference.com`         | Online API reference for dependencies |
| C++ Standard      | `https://en.cppreference.com/w/`    | C++ standard library reference          |
| <Resource Name>   | `<URL>`                             | <Description>                        |

### Specification Documents

| Document          | Path                                | Description                          |
|-------------------|-------------------------------------|--------------------------------------|
| Code Style Guide  | `docs/<style-guide>.md`             | C/C++ coding conventions             |
| Primary Spec      | `docs/<primary-spec>.md`            | Main project specification           |
| <Additional Spec> | `docs/<additional-spec>.md`         | <Description>                        |

### Source Code

| Component         | Path                                | Description                          |
|-------------------|-------------------------------------|--------------------------------------|
| <Library/Module>  | `projects/<name>/`                  | <Description>                        |
| <Tool/App>        | `projects/<name>/`                  | <Description>                        |

### Build System

| File              | Path                                | Description                          |
|-------------------|-------------------------------------|--------------------------------------|
| Build Script/File | `<scripts/build.sh>` or `CMakeLists.txt` | Compiles the project            |
| Clean Script      | `<scripts/clean.sh>`                | Removes build artifacts              |
| Test Command      | `<build/bin/tests>` or `ctest`      | Runs the test suite                  |

### Development Artifacts

| Directory         | Path                                | Description                          |
|-------------------|-------------------------------------|--------------------------------------|
| Steps Files       | `docs/*.steps.md`                   | Projects' steps files (including this one) |
| Step Documents    | `docs/*.steps/*.md`                 | Individual step documents with detailed plans and notes |
| Test Files        | `projects/<name>/tests/`            | C/C++ test cases                     |

## Suggested Development Roadmap

<!--
  Expand on the Implementation Pipeline above with fine-grained sub-steps
  for each phase. This gives the AI a clear sequencing guide. Reference
  specification sections so the AI knows where to look for details.

  Each phase below should correspond to a phase in the Implementation
  Pipeline table. Add as many phases and steps as needed.
-->

Below is a suggested order for implementing <PROJECT_NAME>, based on component
dependencies and the project specification(s).

### Phase 1: <Phase Name>

<Brief description of what this phase establishes.>

| Step | Description                              | Spec Reference         |
|------|------------------------------------------|------------------------|
| 1.1  | <Sub-step description>                   | <Spec section>         |
| 1.2  | <Sub-step description>                   | <Spec section>         |
| 1.3  | <Sub-step description>                   | <Spec section>         |

### Phase 2: <Phase Name>

<Brief description of what this phase establishes.>

| Step | Description                              | Spec Reference         |
|------|------------------------------------------|------------------------|
| 2.1  | <Sub-step description>                   | <Spec section>         |
| 2.2  | <Sub-step description>                   | <Spec section>         |
| 2.3  | <Sub-step description>                   | <Spec section>         |

<!--
  Continue adding phases as needed (Phase 3, 4, ...).
  Each phase should build on the previous one.
-->

## Steps

<!--
  Steps will be added here as development progresses. Each step follows the
  format defined in the Steps Entry Template above. Separate entries with
  horizontal rules (---) for readability.
-->

(Steps will be added here as development progresses. Each step follows the
format defined in **Steps Entry Template** above.)
