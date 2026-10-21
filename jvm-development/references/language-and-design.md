# Java and Kotlin Language and Design

## Compatibility and Repository Conventions

- Treat build files, toolchains, version catalogs, formatter rules, static-analysis config, and
  nearby code as authoritative. Do not assume a JDK, Kotlin, Spring, Maven, or Gradle version.
- Preserve the configured source and bytecode targets. Do not introduce newer language or library
  APIs unless the task includes the corresponding upgrade.
- Follow existing package, naming, injection, annotation, and source-generation conventions.
  Avoid repository-wide formatting or import churn during a focused change.

## Naming

- Never use `record` as a Java or Kotlin value identifier. Rename it to the actual domain concept,
  such as `userEntity`, `databaseRow`, `eventEntry`, or `queryResult`.
- When no stronger formatter exists, use lowercase packages, `PascalCase` types, `camelCase`
  functions/properties/variables, `UPPER_SNAKE_CASE` constants, and avoid wildcard imports.
- Prefer descriptive domain names over abbreviations. Replace long parameter lists with a
  meaningful command, query, options, or parameter object when values travel together.

## Java Defaults

- Prefer immutable state and explicit dependencies. Use records for simple immutable data carriers
  only when they fit the domain and the configured JDK supports them.
- Use try-with-resources for owned closeable resources. Preserve exception causes and throw specific
  exceptions with actionable messages; do not silently swallow failures.
- Preserve the interrupt flag when handling `InterruptedException`. Shut down executors and other
  background resources owned by the changed code.
- Use `Optional` for return values only when absence is part of the established API. Do not add
  `Optional` fields or parameters without a repository-specific convention requiring them.

## Kotlin Defaults

- Prefer `val`, expression-oriented code, null-safe operators, and small focused functions. Use
  `var`, `lateinit`, `!!`, and mutable collections only when their lifecycle or invariant is clear.
- Use data classes for value-like DTOs when consistent with serialization and framework needs.
  Keep domain behavior out of transport-only data classes.
- Preserve structured concurrency. Avoid `GlobalScope`, unowned coroutine scopes, blocking calls in
  suspend paths, and fire-and-forget work without explicit lifecycle and error handling.
- Keep Java interop explicit around platform types, nullability, checked exceptions, and mutable
  collections. Do not hide unsafe conversions behind unchecked casts.

## API and Change Design

- Keep public APIs and serialized shapes stable unless the task explicitly changes the contract.
  Treat field names, enum values, numeric precision, date/time representation, and nullability as
  compatibility-sensitive.
- Keep transport adapters thin, application services responsible for orchestration, domain code
  responsible for business rules, and infrastructure responsible for persistence and integrations
  when the repository follows layered or hexagonal architecture.
- Do not expose persistence entities directly through APIs when the project already separates
  entities, DTOs, commands, queries, or views. Map explicitly enough to preserve contract meaning.
