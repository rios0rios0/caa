<h1 align="center">CAA - Classic Academic Algorithms</h1>
<p align="center">
    <a href="https://github.com/rios0rios0/caa/releases/latest">
        <img src="https://img.shields.io/github/release/rios0rios0/caa.svg?style=for-the-badge&logo=github" alt="Latest Release"/></a>
    <a href="https://github.com/rios0rios0/caa/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/rios0rios0/caa.svg?style=for-the-badge&logo=github" alt="License"/></a>
    <a href="https://github.com/rios0rios0/caa/actions/workflows/default.yaml">
        <img src="https://img.shields.io/github/actions/workflow/status/rios0rios0/caa/default.yaml?branch=main&style=for-the-badge&logo=github" alt="Build Status"/></a>
</p>

A collection of classic sorting algorithms implemented in Java 8 with a built-in instruction-counting benchmarking framework that measures best-case, worst-case, and average-case performance across varying input sizes. Each algorithm counts the exact number of primitive operations (comparisons, swaps, assignments) executed during sorting, providing concrete empirical data for algorithm analysis.

## Features

- **Four Sorting Algorithms** -- HeapSort, InsertionSort, QuickSort, and RadixSort, each implementing a common `Sort` interface
- **Instruction Counting** -- every algorithm tracks the exact number of primitive operations via an `inc()` counter, allowing direct comparison of computational cost
- **Best/Worst Case Demonstration** -- the `demo()` method runs each algorithm with pre-defined best-case and worst-case inputs, printing the sorted output and instruction count
- **Scaling Analysis** -- `demo()` also runs each algorithm with random arrays of doubling sizes (625, 1250, 2500, 5000, 10000, 20000 elements) to observe growth behavior
- **Statistical Benchmarking** -- the `analysis()` method runs 50 iterations across array sizes of 5, 10, 50, 100, 1000, and 10000 elements, computing the median instruction count per size
- **Random Array Generation** -- `Array.generateArray(size)` creates arrays with random integers in the range `[0, size*10)` using `java.util.Random` with `OptionalInt` streams
- **Colorized Console Output** -- ANSI escape code-based colored output (green for success, blue for info, red for errors) via a `Colorize` utility with support for bold, italic, and underline formatting
- **Maven Build** -- standard Maven project structure with `maven-assembly-plugin` for building a self-contained JAR with dependencies

## Sorting Algorithms

### HeapSort

- **Time Complexity**: O(n log n) in all cases
- **Implementation**: builds a max-heap from the input array using `heapify()`, then repeatedly extracts the maximum element. The `heapify()` method recursively compares parent nodes with left (`2*i+1`) and right (`2*i+2`) children, swapping the largest upward.
- **Best Case**: the input already forms a valid max-heap (the heap structure is pre-built), minimizing `heapify` operations
- **Worst Case**: the input requires full heap construction, maximizing the number of recursive `heapify` calls

### InsertionSort

- **Time Complexity**: O(n) best case, O(n^2) worst case
- **Implementation**: iterates from the second element forward, shifting elements rightward in the sorted prefix until the correct insertion position is found. The inner `while` loop compares and shifts simultaneously.
- **Best Case**: already sorted array -- the inner loop never executes
- **Worst Case**: reverse-sorted array -- every element shifts all the way to the beginning

### QuickSort

- **Time Complexity**: O(n log n) average, O(n^2) worst case
- **Implementation**: uses Lomuto-style partitioning with the first element as pivot. The `part()` method maintains two indices scanning inward from both ends, swapping elements that are on the wrong side of the pivot. Recursion follows `T(n) = T(n/2) + T(n/2) + n`.
- **Best Case**: the pivot consistently divides the array in half
- **Worst Case**: the pivot is always the smallest or largest element (e.g., already sorted input)

### RadixSort

- **Time Complexity**: O(d * n) where d is the number of digits
- **Implementation**: processes integers digit by digit from the least significant to the most significant. For each digit position (`exp = 1, 10, 100, ...`), a counting sort distributes elements into 10 buckets (digits 0-9), then reassembles them. The process repeats until all digits of the maximum value are processed.
- **Concept**: analogous to sorting a deck of 200 numbered cards by placing them into piles 0-9 based on each digit, starting from the ones place. After processing the highest digit, the entire deck is sorted.

## Technologies

| Component | Details |
|-----------|---------|
| **Language** | Java 8 (lambdas, streams, `OptionalInt`) |
| **Build** | Apache Maven 3 with `maven-assembly-plugin` |
| **CI** | GitHub Actions |
| **Output** | ANSI terminal colors via escape sequences |

## Project Structure

```
caa/
├── pom.xml                                                # Maven build configuration (Java 8, assembly plugin)
├── src/
│   └── main/
│       ├── java/
│       │   └── com/rios0rios0/
│       │       ├── Main.java                              # Entry point - runs demos and analysis for all 4 algorithms
│       │       ├── ordination/
│       │       │   ├── Sort.java                          # Interface: int[] sort(int[])
│       │       │   ├── DefaultSort.java                   # Abstract base - instruction counter, demo(), analysis()
│       │       │   ├── HeapSort.java                      # Max-heap based sort with recursive heapify
│       │       │   ├── InsertionSort.java                 # Insertion-based sort with shifting
│       │       │   ├── QuickSort.java                     # Recursive quicksort with first-element pivot
│       │       │   └── RadixSort.java                     # LSD radix sort with counting sort subroutine
│       │       └── utils/
│       │           ├── Array.java                         # Random array generation and printing
│       │           ├── Colorize.java                      # ANSI escape code formatting (bold, italic, underline, colors)
│       │           └── Console.java                       # Colored console output (success, error, info, SYN/ACK)
│       └── resources/
│           └── META-INF/
│               └── MANIFEST.MF                            # JAR manifest
├── LICENSE                                                # MIT License
└── README.md
```

## Class Hierarchy

```
Sort (interface)
  └── DefaultSort (abstract)
        ├── HeapSort
        ├── InsertionSort
        ├── QuickSort
        └── RadixSort
```

`DefaultSort` provides the benchmarking infrastructure:

- `instructions` counter with `inc(n)` to add n operations and `setInstructions(0)` to reset
- `demo(bestCase, worstCase)` -- runs both cases, prints results, then scales from 625 to 20000 elements
- `analysis(times)` -- runs `times` iterations (default 50) across 6 array sizes, computing median instruction counts
- `performance` map (`LinkedHashMap<Integer, Float>`) accumulates instruction counts per array size across iterations

## Installation

```bash
git clone https://github.com/rios0rios0/caa.git
cd caa
mvn clean package
```

## Usage

### Run via Maven

```bash
mvn exec:java -Dexec.mainClass="com.rios0rios0.Main"
```

### Run via JAR

```bash
java -jar target/CAA-1.0.0-jar-with-dependencies.jar
```

### Sample Output

```
[OK] Starting the demonstrations...
[INFO] HEAP SORT
3 4 5 7 8 9 10 11 15 20
Best Case - Instructions: 42
20 15 11 10 9 8 7 5 4 3
Worst Case - Instructions: 56
Elements: 625
Instructions: 12847
...
[OK] Starting analysis...
[INFO] INSERTION SORT
5 random elements (50 times). Median of instructions: 22
10 random elements (50 times). Median of instructions: 98
50 random elements (50 times). Median of instructions: 2145
100 random elements (50 times). Median of instructions: 8567
1000 random elements (50 times). Median of instructions: 846523
10000 random elements (50 times). Median of instructions: 84652312
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).
