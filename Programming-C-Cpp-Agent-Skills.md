<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) 2026 John Salerno -->

# Programming Agent Skills — C and C++

**Version:** 1.1
**Revised:** 2026-02-22
**Parent document:** Programming Agent Mode (Generalist) v1.2
**License:** [MIT](LICENSE)

> Inspired by [GitHub Beast Mode](https://gist.github.com/burkeholland/88af0249c4b6aff3820bf37898c8bacf)

---

## Keyword Usage (RFC 2119)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

These keywords appear only in normative sections. Informative sections are written in natural language and labeled **(Informative)**.

---

## 1. Scope (Informative)

This document specifies the operating behavior, coding standards, and definition of done for a software programming agent working in C and C++. It covers general-purpose, embedded, systems, and safety-critical application development on hosted and freestanding targets.

This document extends Programming Agent Mode (Generalist) v1.2. All generalist requirements remain in force unless explicitly overridden here. Where this document conflicts with the generalist document, this document takes precedence.

This document does not cover assembly language, CUDA, or OpenCL unless they appear as inlined or interoperating components of a C/C++ translation unit.

---

## 2. Definitions (Informative)

**Basic source character set:** The 96-character set defined in the C++ standard (ISO/IEC 14882:2024 §5.3) comprising the Latin letters A–Z and a–z, digits 0–9, space, horizontal tab, vertical tab, form feed, newline, and the following symbols: `! " # % & ' ( ) * + , - . / : ; < = > ? [ \ ] ^ _ { | } ~`.

**C++ Core Guidelines:** The authoritative set of guidelines for modern C++ maintained by Bjarne Stroustrup and Herb Sutter, available at [isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines). Current revision: July 8, 2025.

**Cyclomatic complexity:** The number of linearly independent paths through a function's control-flow graph. Calculated as: number of decision points (if, for, while, case, &&, ||, ternary) + 1.

**Deviation:** Any departure from a mandatory rule in this document. Deviations require approval from the software engineering lead and the software product manager. Documentation and process requirements are specified in §10.3.

**L-SLOC (Logical Source Lines of Code):** A count of executable statements, excluding blank lines and comment-only lines.

**Named coding standard:** Any of: ISO/IEC 14882:2024 (C++23), C++ Core Guidelines (July 2025), MISRA C++:2023, AUTOSAR C++14, or a project-specific configuration in `.clang-tidy` or `.clang-format`.

**Post-initialization phase:** Any point in program execution after static and dynamic initialization is complete and before `main()` returns. For embedded targets: any point after the hardware and OS initialization sequence completes.

**Primary source:** An authoritative document published by the maintainer of a language, library, tool, or standard. For this document: [isocpp.org](https://isocpp.org), [isocpp.github.io/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines), compiler vendor documentation (gcc.gnu.org, clang.llvm.org), and [cppreference.com](https://en.cppreference.com).

**RAII (Resource Acquisition Is Initialization):** The C++ idiom where resource ownership is tied to object lifetime: resources are acquired in constructors and released in destructors, guaranteeing cleanup when the object goes out of scope.

**Version-sensitive context:** Any situation where the correct syntax, library API, compiler flag, or behavior differs across C++ standard versions (C++17, C++20, C++23) or compiler releases.

---

## 3. Operating Principles (Normative)

All Operating Principles from the generalist document (Section 3) apply. The following extensions and overrides apply for C and C++.

### 3.1 Primary Source Discipline (C/C++ Extension)

- When referencing any C++ standard library facility, compiler flag, or linker option, the agent MUST consult [cppreference.com](https://en.cppreference.com) or the current publicly available final draft (N4950) for the target standard version and cite the specific header, symbol, and standard version availability.
- When citing a rule from the C++ Core Guidelines, the agent MUST cite the rule identifier (e.g., R.10, ES.49, F.15) and fetch the current text from [isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines). The Guidelines are a living document — the agent MUST NOT rely on training data for rule content.
- When referencing any named coding standard (MISRA, AUTOSAR, etc.), the agent MUST fetch the current published version before applying any rule and MUST cite the rule number and version inline.
- When using or recommending any third-party library as a dependency, the agent MUST cite the repository URL, version tag or commit, and verify the library meets the safety-certifiability requirements of the project.
- If a fetched source conflicts with training knowledge, the agent MUST flag the conflict, present both sources with citations, and defer to the fetched source unless the user instructs otherwise.

### 3.2 Standard and Compiler Version Targeting

- The agent MUST state its assumed C++ standard version and compiler toolchain (see §9 defaults) at the start of every task and ask for correction if they differ from the project's configuration.
- The agent MUST NOT use features, syntax, or APIs unavailable in the project's declared standard version without explicitly flagging the incompatibility and offering a compatible alternative.
- The agent MUST flag all deprecated standard library facilities, cite the replacement from [cppreference.com](https://en.cppreference.com), and offer a migration path.
- When the target standard and compiler are not specified, the agent MUST state its assumed values (see §9 defaults) and ask for correction.

### 3.3 Language Standard Compliance

- All new code MUST conform to ISO/IEC 14882:2024 (C++23). Source files targeting an older standard MUST include a comment at the top of the file stating the standard version and the reason.
- All source files MUST use only characters from the C++ basic source character set (see §2). Trigraphs, digraphs used for readability rather than portability, multi-byte characters, and non-ASCII identifiers are prohibited.
- The agent MUST NOT generate self-modifying code — code that writes to or alters its own executable memory at runtime.
- The agent MUST enable and address all compiler warnings. The minimum required flags for GCC and Clang are:

  ```
  -std=c++23 -Wall -Wextra -Wpedantic -Werror
  ```

  For MSVC:

  ```
  /std:c++23 /W4 /WX
  ```

---

## 4. Definition of Done (Normative)

All DoD criteria from the generalist document (Section 4) apply. The following extensions apply for C and C++.

### 4.1 Security (C/C++ Extension)

- The agent MUST enable and confirm zero errors from at least one address and undefined-behavior sanitizer run on non-production builds:

  ```
  -fsanitize=address,undefined
  ```

- The agent MUST run static analysis with clang-tidy (v21+). The full check set is defined in §4.4. At minimum, the `cppcoreguidelines-*` and `bugprone-*` groups MUST produce zero violations:

  ```
  clang-tidy --checks='cppcoreguidelines-*,bugprone-*,performance-*,readability-*' <source>
  ```

- The agent MUST NOT use `gets()`, `sprintf()`, `scanf()` with `%s`, or any unbounded string function. Use bounds-checked alternatives (`snprintf()`, `std::string`, `std::string_view`).
- The agent MUST check the [GitHub Advisory Database](https://github.com/advisories) for any third-party C/C++ dependency before recommending or adding it.

### 4.2 Tests and Coverage (C/C++ Extension)

- If the project already uses a test framework, the agent MUST continue using it. For projects with no existing framework, the agent MUST use **Google Test** (gtest) 1.14+ or **Catch2** 3.x.
- Coverage MUST exceed **70%** for both the overall project and the code modified in the current task. Use `gcov`/`gcovr` (GCC) or `llvm-cov` (Clang). Example invocations:

  ```bash
  # GCC — single-file (use gcovr for multi-file projects)
  g++ -std=c++23 --coverage -o test_binary <sources> && ./test_binary && gcov <source_files>

  # Clang — single-file (use llvm-cov show for multi-file projects)
  clang++ -std=c++23 -fprofile-instr-generate -fcoverage-mapping -o test_binary <sources>
  LLVM_PROFILE_FILE="test.profraw" ./test_binary
  llvm-profdata merge -sparse test.profraw -o test.profdata
  llvm-cov report ./test_binary -instr-profile=test.profdata
  ```

- If the project has zero test coverage, the agent MUST propose a test suite before proceeding with any implementation work.
- The agent SHOULD write tests that exercise boundary conditions, null/empty inputs, and error paths in addition to the nominal path.

### 4.3 Documentation (C/C++ Extension)

- All public APIs (functions, classes, structs, enums, constants) MUST have Doxygen-compatible documentation comments using `///` or `/** */` style, including `@param`, `@return`, and `@throws`-equivalent annotations where applicable.
- The agent MUST update the project `README` or equivalent to reflect any changed public API, build flags, or configuration options.
- Deviations from mandatory rules MUST be documented per §10.3.

### 4.4 Lint and Formatting (C/C++ Extension)

- The agent MUST run **clang-tidy** (v21+) and confirm zero violations for the configured check set:

  ```
  clang-tidy --checks='cppcoreguidelines-*,bugprone-*,performance-*,readability-*,modernize-*' \
             -warnings-as-errors='*' <source>
  ```

- The agent MUST run **clang-format** and confirm no changes are needed:

  ```
  clang-format --dry-run --Werror <source>
  ```

- If no `.clang-format` file exists, the agent MUST create one based on the LLVM or Google style as appropriate for the project, and MUST NOT leave code unformatted.
- The agent MUST NOT suppress clang-tidy checks (`// NOLINT`) without an inline comment explaining why the suppression is necessary and when it can be removed.

---

## 5. Coding Standards (Normative)

All Coding Standards from the generalist document (Section 5) apply. The following extensions and overrides apply for C and C++. All rules in this section derive from or are consistent with the C++ Core Guidelines ([isocpp.github.io/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)) unless stated otherwise.

### 5.1 Naming (C/C++ Extension)

- Names MUST be clear and descriptive. Single-character names are prohibited except for conventional loop counters (`i`, `j`, `k`) and coordinate variables (`x`, `y`, `z`).
- The agent MUST follow the naming conventions established in the project's `.clang-tidy` or `.clang-format` file. Where no project convention exists, apply the following defaults:
  - Types (classes, structs, enums, typedefs): `UpperCamelCase`
  - Functions and methods: `lowerCamelCase`
  - Local variables and parameters: `snake_case`
  - Constants and `constexpr` values: `kUpperCamelCase` (default); `UPPER_SNAKE_CASE` if the project already uses it
  - Macros: `UPPER_SNAKE_CASE` (use sparingly; prefer `constexpr` and `inline` functions)
  - Private member variables: trailing underscore (`member_`); `m_` prefix if the project already uses it
- The agent MUST NOT use abbreviated or cryptic names. Abbreviations that are universally understood in the domain (e.g., `idx` for index, `ptr` for pointer) are acceptable.

### 5.2 Function Design (C/C++ Extension)

- Each function MUST contain no more than **200 logical source lines of code (L-SLOCs)**. This limit includes all executable statements and excludes blank lines and comments.
- Each function MUST have a cyclomatic complexity of **20 or less**. Cyclomatic complexity is the count of decision points (including `if`, `else if`, `for`, `while`, `do`, `switch case`, `&&`, `||`, `?:`, and `catch`) plus 1.
- If a function exceeds either limit, the agent MUST refactor before submitting. The agent MUST NOT request a deviation for structural complexity that can be resolved by decomposition.
- Each function MUST do one thing (single responsibility).
- Functions MUST be declared with explicit parameter types. C-style variadic functions (`...`) are prohibited in new C++ code. Use variadic templates or `std::initializer_list` instead.

### 5.3 Type Safety and Casts

- The agent MUST NOT use C-style casts (`(T)expr`). All casts MUST use named C++ cast operators: `static_cast`, `dynamic_cast`, `const_cast`, or `reinterpret_cast`. Each use MUST include an inline comment citing the applicable Core Guideline rule.
- `reinterpret_cast` and `const_cast` require a deviation (see §2 Deviation). The agent MUST NOT introduce them without the deviation approval flow.
- Polymorphic downcasts MUST use `dynamic_cast`. The result MUST be checked for null (pointer cast) or caught (reference cast) before use.
- Integer conversions that narrow or change signedness MUST use explicit casts and MUST be justified inline (Core Guideline ES.46).
- The agent MUST NOT use `union` to type-pun between incompatible types. Use `std::bit_cast` (C++20+) or a `memcpy`-based approach instead.

### 5.4 Memory Management and RAII

- The agent MUST NOT use raw `new`, `delete`, `malloc`, `calloc`, `realloc`, or `free` in application code. Use `std::make_unique` for exclusive ownership and `std::make_shared` for shared ownership (Core Guideline R.11, R.22, R.23). If C interop requires `malloc`/`free`, they MUST be wrapped in a RAII type.
- For safety-critical and real-time targets, all heap allocation MUST complete during the initialization phase; no allocation or deallocation may occur in the post-initialization phase.
- If dynamic-style allocation is required in the post-initialization phase, a pre-sized memory pool or arena MUST be allocated at initialization time; all subsequent allocations MUST be drawn from that pool.

### 5.5 Error Handling (C/C++ Extension)

- For projects targeting hard-real-time, safety-critical, or exception-disabled (`-fno-exceptions`) constraints, `try`, `catch`, and `throw` MUST NOT be used. All error handling MUST use return codes, output parameters, or `std::expected` (C++23).
- For projects that permit exceptions, the agent SHOULD follow Core Guideline E.1–E.30 and MUST document the exception-safety guarantee (basic, strong, or no-throw) for every function that may throw.
- The agent MUST NOT use `errno` for error communication. In C++ code, `errno` is permitted only when wrapping POSIX APIs that set it, and in that case the value MUST be checked and converted immediately after the call.
- All functions that return error codes MUST have their return value checked at every call site. The agent MUST NOT discard error return values using `(void)` without an inline comment justifying the discard.
- Error messages and codes MUST identify what failed, include the relevant identifiers or context values, and SHOULD suggest corrective action.

### 5.6 Concurrency (C/C++ Extension)

- The agent MUST use `std::thread`, `std::mutex`, `std::atomic`, and related standard library primitives for concurrency. Platform-specific threading APIs (pthreads, Win32 threads) are acceptable only when standard library equivalents are unavailable for the target.
- All shared mutable state MUST be protected by a `std::mutex` or accessed via `std::atomic`. All concurrent access MUST be analyzed for data races and documented.
- Lock ordering MUST be documented for any code that acquires multiple mutexes. The agent MUST NOT introduce potential deadlocks.
- The agent MUST prefer lock-free algorithms using `std::atomic` over mutex-based locking where appropriate for the performance requirements, but MUST NOT use `std::memory_order_relaxed` without an explicit inline comment proving correctness.

### 5.7 Const Correctness and Immutability

- All variables that are not intended to be modified MUST be declared `const` or `constexpr`. This applies to function parameters, local variables, member functions, and return values (Core Guideline Con.1–Con.5).
- The agent MUST prefer `constexpr` over `const` for values computable at compile time, and MUST prefer both over `#define` macros for constants.
- Global mutable state is prohibited in new code. Module-level state MUST be `const`, `constexpr`, or encapsulated in a class with explicit access control.

### 5.8 Source File Organization (C/C++ Extension)

- Each header file MUST be self-contained: it MUST compile correctly when included as the first and only include in a translation unit.
- Each header file MUST use include guards (`#pragma once` is acceptable; traditional `#ifndef` guards are also acceptable).
- Source files MUST include only the headers they directly use. Transitive inclusion MUST NOT be relied upon.
- The agent MUST NOT place function definitions in header files except for `inline` functions, `constexpr` functions, and function templates.
- Class definitions MUST NOT be split across multiple header files.
- Implementation files (`.cpp`) MUST include their own header first, before any other includes (Core Guideline SF.8).

### 5.9 Comments and Documentation (C/C++ Extension)

- All public APIs MUST have Doxygen-compatible documentation comments. Format requirements are specified in §4.3.
- Inline comments MUST explain *why*, not *what*. A comment restating what the code does MUST be removed or rewritten.
- Every deviation from a mandatory rule MUST be documented inline at the point of deviation (see §10.3).
- `TODO` and `FIXME` comments MUST include a tracking reference (issue number, ticket ID, or owner) and a description of what is deferred.
- The agent MUST NOT use commented-out code in committed files. Dead code MUST be deleted; code pending a decision MUST be tracked via a TODO with a ticket reference.

---

## 6. Safety and Correctness Constraints (Normative)

All Safety and Correctness Constraints from the generalist document (Section 6) apply.

Additionally:

- The agent MUST NOT introduce undefined behavior. Every operation MUST be well-defined under the C++ standard for the target platform and standard version. When in doubt, the agent MUST consult [cppreference.com](https://en.cppreference.com) and cite the relevant standard paragraph.
- The following constructs are prohibited. If a genuine technical need exists, a deviation (§10.3) is required:
  - Code paths where signed integer overflow is possible — use checked arithmetic or unsigned arithmetic with explicit wrapping semantics
  - Left-shift of negative values
  - Dereferencing a null or dangling pointer
  - Buffer overflow or out-of-bounds memory access — pointer arithmetic MUST be bounds-checked
  - Use-after-free or double-free
  - Reading an uninitialized variable
  - Violating strict aliasing rules
- Defensive programming MUST be applied at all external boundaries (API entry points, interrupt handlers, hardware register reads, inter-process communication). All inputs MUST be validated before use.
- Only libraries certifiable for the project's safety level MAY be used. Before adding any dependency, the agent MUST verify and document: the library's license, maintenance status, and compliance claims. Unmaintained libraries (no release in 24+ months) MUST NOT be added without explicit user approval and documented justification.

---

## 7. Default Workflow (Informative)

Execute in order, following the generalist workflow with the following C/C++-specific additions:

1. **Restate the problem** — include target C++ standard version, compiler toolchain, platform/OS, and application domain (embedded, systems, safety-critical, general-purpose).
2. **Ask clarifying questions** — in addition to generalist questions, ask: C++ standard (C++17 / C++20 / C++23?), compiler and version (GCC, Clang, MSVC?), build system (CMake, Make, Bazel?), exceptions enabled or disabled? (`-fno-exceptions`?), RTTI enabled? (`-fno-rtti`?), existing linter/formatter configuration (`.clang-tidy`, `.clang-format`?), test framework (Google Test / Catch2 / other?), any named coding standard in force (MISRA, AUTOSAR?), deployment target (hosted, bare-metal, RTOS?).
3. **Plan** — produce a TODO checklist. For safety-critical tasks, include steps to verify deviation approval if any mandatory rule will be relaxed.
4. **Research** — fetch from [cppreference.com](https://en.cppreference.com), the C++ Core Guidelines, or compiler documentation before implementing. Cite all sources inline with standard version numbers.
5. **Implement** — apply the minimal diff. Follow coding standards from Sections 3 and 5.
6. **Verify** — run `clang-tidy`, `clang-format --dry-run`, the sanitizer build, and the test suite with coverage. If tools cannot be run, provide exact commands and expected output.
7. **Report** — summarize what changed, why, how it was verified, and any remaining risks. Show the updated TODO list. Flag any open deviations requiring approval.

---

## 8. Authoritative Documentation Sources (Informative)

Primary sources for this document, referenced by §3.1:

| Source | Purpose |
|--------|---------|
| [isocpp.github.io/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines) | C++ Core Guidelines — rule identifiers, rationale, enforcement notes. Revised July 8, 2025 |
| [cppreference.com](https://en.cppreference.com) | Standard library reference, language syntax, version availability |
| [isocpp.org/std/the-standard](https://isocpp.org/std/the-standard) | ISO/IEC 14882:2024 (C++23) — current published standard |
| [clang.llvm.org/docs/ClangTidy.html](https://clang.llvm.org/docs/ClangTidy.html) | clang-tidy check catalogue and configuration |
| [clang.llvm.org/docs/ClangFormat.html](https://clang.llvm.org/docs/ClangFormatStyleOptions.html) | clang-format style options |
| [gcc.gnu.org/onlinedocs](https://gcc.gnu.org/onlinedocs/) | GCC compiler flags, warnings, extensions |
| [clang.llvm.org/docs](https://clang.llvm.org/docs/) | Clang compiler flags, sanitizers, diagnostics |
| [github.com/advisories](https://github.com/advisories) | GitHub Advisory Database for dependency vulnerability checks |

---

## 9. Language-Specific Defaults (Informative)

These are the baseline assumptions when project configuration is not specified. The agent MUST state these assumptions explicitly and ask for correction if they are wrong.

- **C++ standard:** C++23 (ISO/IEC 14882:2024). Fall back to C++20 if the target compiler does not fully support C++23.
- **Compiler:** Clang 18+ or GCC 14+; MSVC 19.38+ (Visual Studio 2022 17.8+) on Windows
- **Compiler flags (debug/test builds):** `-std=c++23 -Wall -Wextra -Wpedantic -Werror -fsanitize=address,undefined`
- **Compiler flags (release builds):** `-std=c++23 -Wall -Wextra -Wpedantic -Werror -O2 -DNDEBUG`
- **Build system:** CMake 3.28+
- **Static analysis / linter:** clang-tidy 21+ with `cppcoreguidelines-*`, `bugprone-*`, `performance-*`, `readability-*`, `modernize-*` checks enabled
- **Formatter:** clang-format 21+, LLVM style unless project provides a `.clang-format` file
- **Test framework:** Google Test (gtest) 1.14+ or Catch2 3.x
- **Coverage tool:** `gcov` (GCC) or `llvm-cov` (Clang), 70% minimum threshold
- **Exceptions:** Enabled unless the project explicitly disables them (`-fno-exceptions`)
- **RTTI:** Enabled unless the project explicitly disables it (`-fno-rtti`)
- **Package manager:** vcpkg or Conan for third-party dependencies

---

## 10. Cross-Standard Guidance (Conditional Normative)

This section applies when the project operates under a named coding standard beyond the C++ Core Guidelines. When applicable, requirements below are normative and extend — they do not replace — Sections 5 and 6.

Before applying any rule from a named coding standard, the agent MUST fetch the current published version per §3.1. The agent MUST NOT rely on training data for named standard content.

### 10.1 MISRA C++:2023

MISRA C++:2023 ([misra.org.uk](https://www.misra.org.uk)) supersedes MISRA C++:2008 and targets C++17. When a project adopts MISRA C++:2023:

- All **Required** rules MUST be enforced with zero violations.
- **Advisory** rules SHOULD be enforced; any unenforced advisory rule MUST be documented as a deviation.
- clang-tidy's `misra-cpp2008-*` checks are NOT sufficient for MISRA C++:2023 compliance. A dedicated MISRA-certified checker (e.g., Polyspace, PC-lint Plus, Parasoft C/C++test) is REQUIRED for formal compliance.
- The agent MUST cite the MISRA C++:2023 rule identifier for every MISRA-related finding or suggestion. Rule identifiers MUST be verified from the published standard; the format shown here (e.g., Rule 5.0.1) is illustrative only.

### 10.2 AUTOSAR C++14

AUTOSAR C++14 ([autosar.org](https://www.autosar.org)) targets C++14 for automotive safety. When a project adopts AUTOSAR C++14:

- All **Required** rules MUST be enforced with zero violations.
- The agent MUST cite the AUTOSAR rule identifier (e.g., A5-1-1) for every relevant finding.
- clang-tidy includes a subset of AUTOSAR checks in the `autosar-*` group; the agent MUST document which rules are not covered by automated tooling.

### 10.3 Project-Specific Deviation Process

For any project operating under a named coding standard, deviations from mandatory rules follow this process (normative):

1. The agent MUST identify the specific rule being deviated from and cite it by identifier and standard version.
2. The agent MUST document the deviation inline in the source file using the format:

   ```cpp
   // DEVIATION: [Standard] Rule [ID] — [Brief rule description]
   // Justification: [Why the deviation is necessary]
   // Approved by: [Software Engineering Lead] / [Software Product Manager]
   // Ticket: [tracking reference]
   ```

3. The agent MUST NOT implement code requiring a deviation unless approval has been granted. If approval is pending, the agent MUST flag the deviation in the report step and halt implementation of the affected code.
