# Copilot Instructions for CAA

## Project Overview

**CAA (Classic Academic Algorithms)** is a Java 8 Maven project that implements four classic sorting algorithms with a built-in instruction-counting benchmarking framework. It measures best-case, worst-case, and average-case performance across varying input sizes by counting the exact number of primitive operations (comparisons, swaps, assignments) executed during sorting.

## Repository Structure

```
caa/
├── .github/
│   └── workflows/
│       └── default.yaml          # CI pipeline (delegates to shared java-maven workflow)
├── src/
│   └── main/
│       ├── java/
│       │   └── com/rios0rios0/
│       │       ├── Main.java                        # Entry point – runs demos and analysis for all 4 algorithms
│       │       ├── ordination/
│       │       │   ├── Sort.java                    # Interface: int[] sort(int[])
│       │       │   ├── DefaultSort.java             # Abstract base – instruction counter, demo(), analysis()
│       │       │   ├── HeapSort.java                # Max-heap sort with recursive heapify
│       │       │   ├── InsertionSort.java           # Insertion sort with element shifting
│       │       │   ├── QuickSort.java               # Recursive quicksort with first-element pivot
│       │       │   └── RadixSort.java               # LSD radix sort with counting sort subroutine
│       │       └── utils/
│       │           ├── Array.java                   # Random array generation and printing
│       │           ├── Colorize.java                # ANSI escape code formatting
│       │           └── Console.java                 # Colored console output helpers
│       └── resources/
│           └── META-INF/
│               └── MANIFEST.MF                      # JAR manifest
├── pom.xml                                          # Maven build configuration
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Technology Stack

| Component    | Details                                                     |
|--------------|-------------------------------------------------------------|
| **Language** | Java 8 (lambdas, streams, `OptionalInt`)                   |
| **Build**    | Apache Maven 3 with `maven-assembly-plugin`                 |
| **CI**       | GitHub Actions via `rios0rios0/pipelines` shared workflow   |
| **Output**   | ANSI terminal colors via escape sequences (`Colorize`)      |

## Build, Test, and Run Commands

### Build

```bash
mvn clean package
# Produces: target/CAA-0.1.0-jar-with-dependencies.jar
# Typical duration: ~10–15 seconds
```

### Run

```bash
# Run via Maven exec plugin
mvn exec:java -Dexec.mainClass="com.rios0rios0.Main"

# Run via assembled JAR
java -jar target/CAA-0.1.0-jar-with-dependencies.jar
```

### Test

```bash
mvn test
```

### Clean

```bash
mvn clean
```

## Architecture and Design Patterns

### Class Hierarchy

```
Sort (interface)
  └── DefaultSort (abstract)
        ├── HeapSort
        ├── InsertionSort
        ├── QuickSort
        └── RadixSort
```

- **`Sort` interface** – defines `int[] sort(int[])` as the contract for all sorting implementations.
- **`DefaultSort` abstract class** – provides the benchmarking infrastructure shared by all algorithms:
  - `instructions` counter with `inc(n)` to accumulate operations and `setInstructions(0)` to reset.
  - `demo(bestCase, worstCase)` – runs best/worst case inputs, prints results, then runs scaling tests from 625 to 20 000 elements (doubling each step).
  - `analysis(times)` – runs `times` (default 50) iterations across 6 array sizes (5, 10, 50, 100, 1 000, 10 000), computing the median instruction count per size.
  - `performance` map (`LinkedHashMap<Integer, Float>`) accumulates instruction counts per array size across iterations.
- Each algorithm overrides `sort(int[])` and calls `inc()` inside every primitive operation to count instructions.

### Utilities

- **`Array`** – generates random integer arrays with values in `[0, size*10)` using `java.util.Random` and `OptionalInt` streams.
- **`Colorize`** – builds ANSI escape sequences supporting bold, italic, underline, and colors (green, blue, red).
- **`Console`** – provides `success()`, `error()`, and `info()` wrappers that print colorized messages to stdout.

## Sorting Algorithms

| Algorithm      | Best Case   | Worst Case   | Average Case | Key Detail                                  |
|----------------|-------------|--------------|--------------|---------------------------------------------|
| HeapSort       | O(n log n)  | O(n log n)   | O(n log n)   | Max-heap; recursive `heapify`              |
| InsertionSort  | O(n)        | O(n²)        | O(n²)        | Shift-based; inner loop counts operations  |
| QuickSort      | O(n log n)  | O(n²)        | O(n log n)   | First-element pivot; Lomuto-style partition |
| RadixSort      | O(d·n)      | O(d·n)       | O(d·n)       | LSD digit-by-digit counting sort           |

## CI/CD Pipeline

The workflow in `.github/workflows/default.yaml` delegates to the shared reusable pipeline:

```
rios0rios0/pipelines/.github/workflows/java-maven.yaml@main
```

It triggers on:
- Pushes to `main`
- Any tag push
- Pull requests targeting `main`
- Manual dispatch (`workflow_dispatch`)

Required permissions: `security-events: write`, `contents: write`.

## Development Workflow

1. Fork and clone the repository.
2. Create a feature branch: `git checkout -b feat/my-change`
3. Make changes following the conventions below.
4. Build and verify: `mvn clean package`
5. Run the application to manually verify output: `mvn exec:java -Dexec.mainClass="com.rios0rios0.Main"`
6. Run tests: `mvn test`
7. Commit following the [commit conventions](https://github.com/rios0rios0/guide/wiki/Life-Cycle/Git-Flow) (Conventional Commits).
8. Open a pull request against `main`.

## Coding Conventions

- **Java 8 only** – use lambdas, streams, and `Optional`/`OptionalInt` where appropriate; avoid features from later Java versions.
- **Instruction counting** – every new sorting algorithm must call `inc()` (or `inc(n)`) inside each primitive operation (comparisons, swaps, assignments) to maintain accurate benchmarking.
- **`Sort` interface** – all sorting implementations must implement `Sort` and extend `DefaultSort`.
- **Naming** – class names use PascalCase; method and variable names use camelCase.
- **Output** – use `Console.showMsgSuccess()`, `Console.showMsgInfo()`, and `Console.showMsgError()` for colored output; do not use raw `System.out.println` in algorithm or demo code.
- **Package structure** – algorithm classes belong in `com.rios0rios0.ordination`; utility classes belong in `com.rios0rios0.utils`.
- **No external dependencies** – the project has no runtime dependencies beyond the JDK; keep it dependency-free.

## Common Tasks

### Adding a new sorting algorithm

1. Create a new class in `src/main/java/com/rios0rios0/ordination/` extending `DefaultSort` and implementing `Sort`.
2. Override `sort(int[])` and call `inc()` inside every primitive operation.
3. Define meaningful best-case and worst-case input arrays.
4. Register the algorithm in `Main.java` alongside the existing four.

### Updating the version

Update the `<version>` tag in `pom.xml` and the JAR filename reference in `README.md` and `CONTRIBUTING.md`.

## Troubleshooting

- **`ClassNotFoundException` when running the JAR** – ensure you used `mvn clean package` (not just `mvn compile`) so the assembly plugin produces the fat JAR with the correct manifest.
- **ANSI colors not rendered** – the terminal must support ANSI escape codes. On Windows, use Windows Terminal or enable virtual terminal processing.
- **`mvn exec:java` not found** – the `exec-maven-plugin` is not declared in `pom.xml`; use the JAR approach instead, or add the plugin explicitly.
