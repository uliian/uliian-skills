---
name: jvm-development
description: Apply Java and Kotlin engineering rules when implementing, modifying, reviewing, debugging, testing, or building JVM projects, including Spring, persistence, Maven, and Gradle work. Do not use for non-JVM projects or conceptual questions that do not involve JVM code or project changes.
---

# JVM Development

Use these defaults together with the user's request and repository-local instructions. Local
conventions may refine general style, architecture, and commands. Do not relax the mandatory
invariants below unless the user explicitly overrides them for the current task.

## Mandatory Invariants

### Prevent N+1 Database Access

- Unless it is genuinely unavoidable, never execute a repository, DAO, mapper, ORM, or direct
  database query from a loop, Java Stream pipeline, Kotlin collection/Sequence pipeline,
  `map`, `flatMap`, `forEach`, per-item callback, or equivalent iteration.
- Treat implicit lazy-loading triggered during iteration as a database query inside the loop.
- Collect the required keys first, query in bulk outside the iteration, index or group the
  results in memory, and then assemble the output in memory.
- If database limits require it, partition a bulk query into a small bounded number of chunks;
  do not fall back to one query per item.
- An exception is allowed only when batching is technically impossible because each query
  depends on the previous result or strict per-item transaction/locking semantics are required.
  Make that constraint and its performance impact explicit before retaining the per-item query.

### Reserve `record`

- Never use `record` as a variable, parameter, field/property, loop variable, lambda parameter,
  destructuring binding, or other value identifier in Java or Kotlin, even where a compiler
  currently accepts it. It conflicts with modern Java's `record` restricted identifier.
- Use a domain-specific name, or a precise alternative such as `entity`, `row`, `item`,
  `entry`, `result`, `payload`, or `storedValue`.

### Avoid Unrequested Defensive Validation

- Unless the user explicitly requests it, do not add defensive programming or parameter-validity
  checks to business-logic code. Assume callers satisfy the established contract instead of adding
  proactive null, blank, range, format, enum, or similar guards and fallback branches.
- Parameter validation is appropriate at client-input boundaries such as controllers. Prefer
  non-invasive, declarative mechanisms such as Bean Validation annotations and `@Valid` over
  manual validation branches.
- Do not remove existing validation unless the requested change requires it.

## Workflow

1. Read the applicable project and directory `AGENTS.md` files plus build manifests and CI
   configuration. Determine the actual JDK, Kotlin, framework, Maven/Gradle, and module setup.
2. Inspect the affected path end to end. For persistence-backed collection flows, explicitly
   check for direct queries and implicit lazy loads inside iteration before editing.
3. Make the smallest coherent change while preserving module boundaries, public contracts,
   serialization formats, nullability conventions, and generated-source workflows.
4. Add or update focused tests when behavior changes. Run the narrowest relevant verification
   first, then module and full checks when proportionate and documented by the repository.
5. Report validation results and any compatibility, migration, concurrency, or external-system
   risk that remains.

## Context-Specific References

- For Java/Kotlin language, API design, error handling, resources, concurrency, and compatibility,
  read [references/language-and-design.md](references/language-and-design.md).
- For database access, ORM behavior, Spring architecture, transactions, configuration, or schema
  work, read [references/persistence-and-spring.md](references/persistence-and-spring.md).
- For Maven/Gradle commands, dependency changes, tests, and verification, read
  [references/testing-and-build.md](references/testing-and-build.md).
