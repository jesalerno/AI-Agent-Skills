<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) 2026 John Salerno -->

# Programming Agent Skills — Swift for Apple Platforms

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

This document specifies the operating behavior, coding standards, and definition of done for a software programming agent working in Swift on Apple platforms: iOS, iPadOS, macOS, tvOS, watchOS, and visionOS.

This document extends Programming Agent Mode (Generalist) v1.2. All generalist requirements remain in force unless explicitly overridden here. Where this document conflicts with the generalist document, this document takes precedence.

This document does not cover Swift on non-Apple platforms (Linux, Windows, Wasm, embedded), Swift package development for cross-platform distribution, or server-side Swift.

---

## 2. Definitions (Informative)

**Apple platforms:** iOS, iPadOS, macOS, tvOS, watchOS, and visionOS. Simulator targets are included.

**Approachable Concurrency:** The Swift 6.2 concurrency mode (`SWIFT_APPROACHABLE_CONCURRENCY` build setting) where code is single-threaded by default, `nonisolated` async functions are non-sending by default, and explicit parallelism requires `@concurrent` opt-in. Introduced in Xcode 26 / Swift 6.2.

**Apple Developer Documentation:** The canonical API reference and conceptual guides at [developer.apple.com/documentation](https://developer.apple.com/documentation/). This is the primary source for all Apple framework APIs, HIG guidance, and sample code.

**HIG:** Apple Human Interface Guidelines — the authoritative design and interaction specification for Apple platforms at [developer.apple.com/design/human-interface-guidelines](https://developer.apple.com/design/human-interface-guidelines/).

**Named coding standard (Swift context):** Any of: Swift API Design Guidelines, Swift.org style guidance, SwiftLint rule directory (v0.63.2+), or project-specific `.swiftlint.yml` configuration.

**Primary source (Swift context):** [swift.org](https://www.swift.org), [developer.apple.com/documentation](https://developer.apple.com/documentation/), [developer.apple.com/develop](https://developer.apple.com/develop/), official Swift Evolution proposals (github.com/swiftlang/swift-evolution), and SwiftLint rule directory (realm.github.io/SwiftLint/rule-directory.html).

**Swift version:** The current stable toolchain release. As of this document revision: **Swift 6.2.3** (released September 2025). Source: [swift.org/install](https://www.swift.org/install).

**SwiftLint:** The canonical Swift linting tool (github.com/realm/SwiftLint). Rule reference: [realm.github.io/SwiftLint/rule-directory.html](https://realm.github.io/SwiftLint/rule-directory.html). Current version: **0.63.2**.

---

## 3. Operating Principles (Normative)

All Operating Principles from the generalist document (Section 3) apply. The following extensions and overrides apply for Swift on Apple platforms.

### 3.1 Primary Source Discipline (Swift Extension)

- When referencing any Apple framework API, the agent MUST consult [developer.apple.com/documentation](https://developer.apple.com/documentation/) and cite the specific framework, symbol, and availability annotation.
- When referencing any Swift language feature, the agent MUST consult [swift.org](https://www.swift.org) or the relevant Swift Evolution proposal and cite the proposal number (e.g., SE-0466).
- When referencing any SwiftLint rule, the agent MUST cite the rule identifier and version from [realm.github.io/SwiftLint/rule-directory.html](https://realm.github.io/SwiftLint/rule-directory.html).
- When using or recommending any third-party Swift package as a dependency, the agent MUST cite the repository URL, the exact tag or commit, and verify the package declares compatibility with the project's Swift version.
- If a fetched source conflicts with training knowledge, the agent MUST flag the conflict, present both sources with citations, and defer to the fetched source unless the user instructs otherwise.
- When Apple Developer Documentation cannot be fetched directly (JavaScript-rendered pages), the agent MUST use web search scoped to `site:developer.apple.com` to locate the specific symbol or guide and cite the URL.

### 3.2 Platform and Version Targeting

- The agent MUST ask for the target platform(s) and minimum deployment target before implementing any platform-specific API.
- The agent MUST use `#available` checks and `@available` annotations when using APIs not available across the full deployment target range.
- The agent MUST NOT use deprecated APIs without explicitly flagging the deprecation, citing the replacement API from Apple Developer Documentation, and offering a migration path.
- The agent MUST state its assumed Xcode and Swift versions (see §9 defaults) at the start of every task and ask for correction if they differ from the project's configuration.

### 3.3 Concurrency Model

- The agent MUST default to Swift 6.2 Approachable Concurrency semantics for new code: single-threaded by default, `@MainActor`-by-default for UI code, `@concurrent` for explicit parallelism opt-in.
- The agent MUST use `async`/`await` and structured concurrency (`async let`, `TaskGroup`) for asynchronous work. The agent MUST NOT introduce `DispatchQueue` or `Thread` usage in new code unless integrating with legacy Objective-C APIs that require it.
- The agent MUST NOT use `nonisolated` to access actor-isolated state from outside the actor without explicit justification.
- The agent MUST ensure `Sendable` conformance is explicit and correct. The agent MUST NOT use `@unchecked Sendable` without a documented justification explaining why the type is safe.
- For UI code, the agent MUST ensure all UI updates occur on the main actor via `@MainActor` annotation or `await MainActor.run {}` where isolation is not already established.

---

## 4. Definition of Done (Normative)

All DoD criteria from the generalist document (Section 4) apply. The following extensions apply for Swift on Apple platforms.

### 4.1 Security (Swift Extension)

In addition to generalist security requirements, the agent MUST:
- Use CryptoKit or Security framework APIs for all cryptographic operations. The agent MUST NOT use deprecated cryptographic APIs. Reference: [developer.apple.com/documentation/cryptokit](https://developer.apple.com/documentation/cryptokit).
- Store sensitive data (credentials, tokens, PII) in the Keychain. The agent MUST NOT store sensitive data in `UserDefaults`, the file system, or in-memory caches that persist across app launches.
- Document any App Transport Security (ATS) exception in a code comment with explicit justification. The agent MUST NOT introduce ATS exceptions without this comment.
- Check each Swift Package Manager dependency against the [GitHub Advisory Database](https://github.com/advisories) before recommending or adding it. The agent SHOULD use `swift-dependency-audit` (github.com/nicklockwood/swift-dependency-audit) if the project has it configured. There is no built-in `swift package audit` command in the Swift toolchain.

### 4.2 Tests and Coverage (Swift Extension)

- The agent MUST use **Swift Testing** (`import Testing`) for all new tests. XCTest is acceptable only when testing UI components that require `XCTestCase` infrastructure or when the project is pinned to a Swift version prior to 5.9.
- The agent MUST use `XCUITest` for UI automation tests when the task involves user-facing flows.
- Coverage MUST exceed **70%** for both the overall project and the code modified in the current task (consistent with generalist DoD).
- If the project has zero test coverage, the agent MUST propose a Swift Testing–based test suite before proceeding with any implementation work.

### 4.3 Documentation (Swift Extension)

- All public APIs MUST be documented using Swift DocC-compatible markup (`///` doc comments with `- Parameters:`, `- Returns:`, `- Throws:` as applicable).
- The agent SHOULD generate or update a DocC catalog when the project already uses one.

### 4.4 Lint and Formatting (Swift Extension)

- The agent MUST run SwiftLint (v0.63.2+) and confirm zero violations against the project's `.swiftlint.yml`, or against the SwiftLint default rule set if no configuration file exists.
- The agent MUST run swift-format or ensure Xcode formatting is applied consistently. If the project has no formatter configured, the agent SHOULD add a `.swift-format` configuration file as part of the task.
- The agent MUST NOT suppress SwiftLint rules inline (`// swiftlint:disable`) without a code comment explaining why the suppression is necessary and when it can be removed.

---

## 5. Coding Standards (Normative)

All Coding Standards from the generalist document (Section 5) apply. The following extensions and overrides apply for Swift on Apple platforms.

### 5.1 Naming (Swift Extension)

- The agent MUST follow the [Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/) for all naming decisions. This is the authoritative source for Swift naming conventions and MUST be fetched and cited when naming decisions are non-obvious.
- Type names MUST be `UpperCamelCase`. Function, variable, and parameter names MUST be `lowerCamelCase`.
- Boolean properties and parameters MUST read as assertions: `isEnabled`, `hasContent`, `canSubmit`.
- Protocol names MUST use a noun when describing a thing (`Collection`, `Sequence`) and an adjective or `-able`/`-ible` suffix when describing a capability (`Sendable`, `Codable`).
- The agent MUST NOT use Objective-C–style prefixes (e.g., `NS`, `UI`, `CG`) on Swift types unless subclassing an Objective-C class requires it.

### 5.2 Value Semantics and Type Design

- The agent MUST prefer `struct` and `enum` over `class` unless reference semantics are required by the design (e.g., identity matters, shared mutable state, Objective-C interop).
- When `class` is used, the agent MUST document why reference semantics are required.
- The agent MUST NOT use `class` solely as a namespace. Use `enum` with no cases as a namespace container.
- The agent SHOULD use `protocol` with associated types to define abstractions, keeping concrete types as implementation details.

### 5.3 Optionals and Error Handling

- The agent MUST NOT use force-unwrap (`!`) except in test code or in contexts where a `nil` value is a programmer error that warrants a crash. Every force-unwrap in non-test code MUST have an inline comment explaining the invariant that guarantees it is safe.
- The agent MUST NOT use `try!` in production code. Use `try` with proper `do/catch`, `try?` only when a `nil` result is semantically meaningful and the error can be safely discarded.
- The agent MUST use `guard let` or `if let` for optional binding rather than optional chaining where the absence of a value requires distinct handling (early return, error, or alternative path).
- Thrown errors MUST conform to a typed error protocol (e.g., a custom `Error`-conforming enum) rather than using `NSError` or untyped `Error` where the error domain is known.

### 5.4 SwiftUI Standards

- The agent MUST use **SwiftUI** as the primary UI framework for new views on all Apple platforms unless the project is explicitly UIKit-only or the required API has no SwiftUI equivalent.
- Views MUST be small and composable. A view body exceeding 50 lines SHOULD be decomposed into subviews or extracted into a separate `View`-conforming type.
- State management MUST use the appropriate property wrapper for the context:
  - `@State` for local view-owned value state
  - `@Binding` for derived mutable state passed from a parent
  - `@Observable` / `@Bindable` (Swift 5.9+) for reference-type view models
  - `@Environment` and `@EnvironmentObject` for dependency injection through the view hierarchy
- The agent MUST NOT use `@ObservableObject`, `@ObservedObject`, `@StateObject`, or `@Published` in new code targeting Swift 5.9+. Use the `@Observable` macro instead.
- The agent MUST provide a `#Preview` macro for every new or modified SwiftUI view.

### 5.5 Swift Data and Persistence

- For new persistence requirements, the agent MUST evaluate **SwiftData** before Core Data, and MUST document the rationale if Core Data is chosen.
- SwiftData model classes MUST use the `@Model` macro and MUST NOT manually manage `ModelContext` lifecycle in views — use the SwiftUI environment integration.
- Any module that accesses the Keychain via raw `SecItem` APIs MUST expose them only through a typed wrapper. No call site outside that wrapper may invoke `SecItem` directly.

### 5.6 Comments and Documentation (Swift Extension)

- Public API documentation requirements are specified in §4.3. In addition, complex SwiftUI view bodies MUST use `// MARK: -` sections to organise logical groupings.
- Concurrency-sensitive code MUST include inline comments explaining the isolation context and any non-obvious `Sendable` or actor decisions.

---

## 6. Safety and Correctness Constraints (Normative)

All Safety and Correctness Constraints from the generalist document (Section 6) apply.

Additionally:
- The agent MUST NOT introduce memory leaks via strong reference cycles. Capture lists (`[weak self]`, `[unowned self]`) MUST be used in closures that are stored or that escape the current scope when a strong cycle would be formed. Every use of `[unowned self]` MUST include an inline comment confirming the referenced object's lifetime exceeds the closure.
- The agent MUST NOT call UI APIs from a background thread or non-main-actor context.

---

## 7. Default Workflow (Informative)

Execute in order, following the generalist workflow with the following Apple-platform–specific additions:

1. **Restate the problem** — include target platform(s), minimum deployment target, and UI framework (SwiftUI / UIKit / AppKit).
2. **Ask clarifying questions** — in addition to generalist questions, ask: Xcode version, Swift version, SwiftLint configuration present?, existing test framework (Swift Testing / XCTest), SwiftData or Core Data?, any Objective-C interop required?
3. **Plan** — produce a TODO checklist. For UI tasks, include a step to verify HIG compliance.
4. **Research** — fetch from [developer.apple.com/documentation](https://developer.apple.com/documentation/), [swift.org](https://www.swift.org), or [developer.apple.com/develop](https://developer.apple.com/develop/) (including sample code) before implementing. Cite all sources inline.
5. **Implement** — apply the minimal diff. Follow coding standards from Sections 3 and 5.
6. **Verify** — run `swift build`, `swift test`, SwiftLint, swift-format, and coverage. For UI changes, verify in Xcode Previews and at least one simulator. Provide exact commands if tools cannot be run.
7. **Report** — summarize what changed, why, how it was verified (including simulator or preview confirmation), and any risks. Show the updated TODO list.

---

## 8. Authoritative Documentation Sources (Informative)

The agent MUST treat the following as primary sources and MUST fetch from them before implementing or advising. Training data alone is insufficient for version-sensitive or API-specific claims.

| Source | Purpose |
|--------|---------|
| [developer.apple.com/documentation](https://developer.apple.com/documentation/) | All Apple framework APIs, availability, deprecation status |
| [developer.apple.com/develop](https://developer.apple.com/develop/) | Platform guides, sample code, technology overviews |
| [swift.org](https://www.swift.org) | Swift language reference, release notes, Swift Evolution proposals |
| [realm.github.io/SwiftLint/rule-directory.html](https://realm.github.io/SwiftLint/rule-directory.html) | SwiftLint rule definitions and configuration |
| [github.com/swiftlang](https://github.com/swiftlang) | Swift compiler, Swift Testing, Swift Evolution, official packages |
| GitHub open-source Swift projects | Reference implementations; MUST cite repo, tag/commit, and Swift version compatibility |

---

## 9. Language-Specific Defaults (Informative)

These are the baseline assumptions when project configuration is not specified. The agent MUST state these assumptions explicitly and ask for correction if they are wrong.

- **Swift version:** 6.2.3 (latest stable as of this document revision)
- **Xcode version:** 26 (paired with Swift 6.2)
- **UI framework:** SwiftUI
- **Concurrency model:** Approachable Concurrency (single-threaded by default, `@concurrent` for opt-in parallelism)
- **Testing framework:** Swift Testing (`import Testing`)
- **Linter:** SwiftLint 0.63.2, default rule set unless `.swiftlint.yml` is present
- **Formatter:** swift-format (preferred when the project can add tooling); Xcode built-in formatter as fallback when swift-format is not configured
- **Persistence:** SwiftData (prefer over Core Data for new projects)
- **Package manager:** Swift Package Manager (prefer over CocoaPods or Carthage for new dependencies)
- **Minimum deployment target:** iOS 17 / macOS 14 / tvOS 17 / watchOS 10 / visionOS 1 (unless specified)

---

## 10. Cross-Standard Guidance (Informative)

For Swift on Apple platforms, the relevant external standards and style guides are:

- **Swift API Design Guidelines** ([swift.org/documentation/api-design-guidelines](https://www.swift.org/documentation/api-design-guidelines/)) — authoritative for naming and API surface design. MUST be fetched and cited for non-obvious decisions.
- **Apple Human Interface Guidelines** ([developer.apple.com/design/human-interface-guidelines](https://developer.apple.com/design/human-interface-guidelines/)) — authoritative for UI/UX decisions on Apple platforms. MUST be consulted for any task involving user-facing design.
- **SwiftLint Rule Directory** ([realm.github.io/SwiftLint/rule-directory.html](https://realm.github.io/SwiftLint/rule-directory.html)) — authoritative for linting standards. MUST be fetched when advising on rule configuration or inline suppressions.

Before applying any rule from these standards, the agent MUST fetch the current published version per §3.1.
