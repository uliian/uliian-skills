# Persistence and Spring

## Mandatory N+1 Rule

Do not issue database queries per element from loops or Java/Kotlin pipelines when a bulk query can
retrieve the required data. This includes hidden ORM lazy loads, repository calls from mapping
functions, MyBatis mapper calls in loops, and helper methods that conceal such calls.

Required approach:

1. Extract and deduplicate the required identifiers.
2. Fetch related rows with a bulk `IN` query, join, fetch join, entity graph, or repository batch
   method outside the iteration.
3. Group or index results in memory by a stable key.
4. Assemble results in memory while preserving required order, duplicates, and missing-data rules.
5. Chunk identifier lists only when database or driver limits require it; keep query count bounded.

Java shape:

```java
var parentIds = parents.stream().map(Parent::id).distinct().toList();
var childrenByParentId = childRepository.findAllByParentIdIn(parentIds).stream()
    .collect(Collectors.groupingBy(Child::parentId));

return parents.stream()
    .map(parent -> assemble(parent, childrenByParentId.getOrDefault(parent.id(), List.of())))
    .toList();
```

Kotlin shape:

```kotlin
val parentIds = parents.map(Parent::id).distinct()
val childrenByParentId = childRepository.findAllByParentIdIn(parentIds).groupBy(Child::parentId)

return parents.map { parent ->
  assemble(parent, childrenByParentId[parent.id].orEmpty())
}
```

Before accepting an exception, verify that batching is technically impossible rather than merely
less convenient. Sequential dependency and strict per-item locking/transaction semantics may be
valid exceptions; document the reason and performance implications.

## Persistence Boundaries

- Keep SQL, ORM query construction, mapper details, and persistence models in the repository's
  persistence or infrastructure layer. Do not build queries in controllers or domain objects.
- Use parameterized SQL and transactions for related writes. Avoid remote calls inside a database
  transaction unless the atomicity and latency tradeoff is intentional.
- Follow the existing database dialect, ID precision, enum, JSON, and date/time conventions. Update
  entities, mappings, migrations, constraints, indexes, fixtures, and conversions together.
- Use the existing migration system and naming scheme. Add forward migrations for schema evolution;
  do not require undocumented manual SQL or rewrite already-applied migrations without explicit
  repository guidance.

## Spring Defaults

- Keep controllers focused on transport, validation, authorization, and mapping. Put orchestration
  in application services and persistence or remote access behind explicit dependencies.
- Follow the project's injection style. For new code without a local convention, prefer constructor
  injection and explicit dependencies.
- Use Bean Validation and typed configuration where supported. Keep endpoints, credentials, tenant
  identifiers, and environment differences in profiles, environment variables, or local config.
- Use focused unit or slice tests for ordinary behavior. Use `@SpringBootTest` only when the test
  genuinely requires the full application context.
- Inspect runners, schedulers, migrations, listeners, and lifecycle hooks before starting an app;
  startup may contact real systems, mutate data, or run indefinitely.
