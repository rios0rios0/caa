# Contributing

Contributions are welcome. By participating, you agree to maintain a respectful and constructive environment.

For coding standards, testing patterns, architecture guidelines, commit conventions, and all
development practices, refer to the **[Development Guide](https://github.com/rios0rios0/guide/wiki)**.

## Prerequisites

- [JDK](https://adoptium.net/) 8+
- [Apache Maven](https://maven.apache.org/) 3+

## Development Workflow

1. Fork and clone the repository
2. Create a branch: `git checkout -b feat/my-change`
3. Install dependencies and build the project:
   ```bash
   mvn clean package
   ```
4. Run the application via Maven:
   ```bash
   mvn exec:java -Dexec.mainClass="com.rios0rios0.Main"
   ```
5. Or run via the assembled JAR:
   ```bash
   java -jar target/CAA-1.0.0-jar-with-dependencies.jar
   ```
6. Run tests:
   ```bash
   mvn test
   ```
7. Commit following the [commit conventions](https://github.com/rios0rios0/guide/wiki/Life-Cycle/Git-Flow)
8. Open a pull request against `main`
