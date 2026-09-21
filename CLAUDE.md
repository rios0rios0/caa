# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

CAA (Classic Academic Algorithms) is a Java 8 Maven project: four classic sorting
algorithms (heap, insertion, quick, radix) behind one `Sort` interface, sharing a
`DefaultSort` base that counts primitive operations to benchmark best/worst/average cases
across input sizes. It is teaching code — clarity and *measurable* complexity matter more
than raw speed.

## Commands

```bash
mvn clean package     # build target/CAA-<version>-jar-with-dependencies.jar (version from pom.xml)
mvn test              # run tests (no suite under src/test/ yet)
mvn exec:java -Dexec.mainClass="com.rios0rios0.Main"   # run demos + analysis
java -jar target/CAA-<version>-jar-with-dependencies.jar
```

`mvn exec:java` needs the `exec-maven-plugin`, which is **not** declared in `pom.xml`; if it
is unavailable, build and run the assembled JAR instead.

## Architecture

`Sort` (`int[] sort(int[])`) is the one contract. `DefaultSort` (abstract) implements it and
owns the benchmark machinery every algorithm inherits:

- an `instructions` counter mutated only through `inc(int)`; reset with `setInstructions(0)`
- `demo(bestCase, worstCase)` — runs both cases, then scales random inputs from 625 to
  20 000 by doubling
- `analysis(times)` (default 50) — medians the instruction count over sizes 5, 10, 50, 100,
  1 000, 10 000, accumulating into the `performance` `LinkedHashMap<Integer, Float>`

Each concrete algorithm overrides `sort` and calls `inc(...)` on every counted primitive
(comparison, swap, assignment). `Main` wires up all four. Utilities live in
`com.rios0rios0.utils`: `Array` (random arrays in `[0, size*10)`, printing), `Colorize`
(ANSI escapes), `Console` (colored `showMsg*` helpers).

## Conventions

- **Java 8 only** — lambdas, streams, `OptionalInt`; nothing from later releases.
- **No runtime dependencies** beyond the JDK. Keep it that way.
- A new algorithm implements `Sort`, extends `DefaultSort`, lives in
  `com.rios0rios0.ordination`, counts every primitive with `inc(...)`, and is registered in
  `Main`. Moving the `analysis` sizes or the 625→20 000 scaling breaks comparability with
  the published numbers.
- Print through `Console`/`Colorize`, not raw `System.out.println`, in algorithm and demo
  code.
- CI (`.github/workflows/default.yaml`) delegates to
  `rios0rios0/pipelines/.github/workflows/maven-library.yaml@main`.

See `.github/copilot-instructions.md` for the full file map, the per-algorithm complexity
table, and troubleshooting notes; the shared engineering standards are the
[rios0rios0/guide wiki](https://github.com/rios0rios0/guide/wiki).

<!-- chlog:start -->
## Changelog (chlog) — MANDATORY

If the repository you are working in uses chlog (a `.chlog.yaml` or `.chlog.yml`
config file, or a `.changes/` directory, exists at the project root), the
following is binding and ALWAYS applies: whenever you make ANY change, you MUST
create a changelog fragment as part of the same change — automatically, without
being asked, before committing.

- Do NOT edit CHANGELOG.md directly; it is generated from fragments.
- Create the fragment with:
  `chlog new --kind <Kind> --body '<past-tense description>'`
- Write an apostrophe inside the single-quoted body as `'\''`.
- Valid kinds: Added, Changed, Deprecated, Removed, Fixed, Security
- Choose the kind that best matches the change (e.g., new feature → Added,
  bug fix → Fixed, behavior change → Changed, removal → Removed, security fix → Security).
- If the change is backward-INCOMPATIBLE with the public API (a breaking
  change), you MUST add the `--breaking` flag:
  `chlog new --kind <Kind> --breaking --body '<past-tense description>'`.
  This is the ONLY thing that triggers a major version bump — the kind alone
  never does (per SemVer, major = incompatible change). When unsure whether a
  change breaks compatibility, ask the user instead of guessing.
- Fragments are YAML files in `.changes/unreleased/`; stage them with your commit.
- `chlog check` fails the build when a fragment is missing — never skip it.
<!-- chlog:end -->
