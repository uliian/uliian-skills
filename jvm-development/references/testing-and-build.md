# Testing and Build

## Toolchain Discovery

- Determine JDK and Kotlin versions, Maven/Gradle versions, modules, profiles, test plugins, and
  quality gates from build files, wrappers, toolchains, CI, and repository instructions.
- Prefer the repository wrapper when present unless local rules require a fixed installation. Never
  switch build systems or silently change `JAVA_HOME`, language levels, repositories, or profiles.
- In a multi-module project, identify the owning module and its dependencies before choosing a task.
  Run the focused module compile or test first, then broader verification when warranted.
- Do not upgrade the JDK, Kotlin, Spring, build plugins, or dependencies merely to bypass an
  unrelated failure. Diagnose compatibility first and keep upgrades explicit.

## Tests

- Follow the configured framework and naming convention. If the project has no stronger convention,
  use JUnit 5, mirror production packages under `src/test/java` or `src/test/kotlin`, and use `*Test`.
- Add a focused regression test for a bug fix when practical. Cover the success path, validation,
  and important failure behavior instead of testing implementation details.
- Prefer unit tests for business rules, focused framework slices for wiring, and integration tests
  only for boundaries that need real infrastructure.
- Mock or fake outbound services in unit tests. Keep tests deterministic and independent of order,
  timezone, local files, real credentials, and live network endpoints.
- Keep credentialed, destructive, device-backed, or external-system integration tests opt-in and
  disabled by default. Do not enable them without confirming the target and side effects.

## Verification and Handoff

- Run the narrowest relevant test first. Then run the affected module's formatter, static analysis,
  compile, and tests, followed by the documented full Maven or Gradle check when proportionate.
- Do not invent commands that are absent from the repository. Do not report a check as successful
  unless it completed with a successful exit status.
- Distinguish failures introduced by the change from reproducible pre-existing failures. Report the
  exact command, outcome, and anything skipped because of environment or authorization limits.
