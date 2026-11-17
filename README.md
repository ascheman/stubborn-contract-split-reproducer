# Stubborn Contract: split local repository reproducer

Environment: Maven 3.9.16, Java 25.
Stubborn Contract: 0.2.2.

Run these commands from this directory. Maven must be
installed. The example uses the default local repository location.

## Installation

```sh
mvn -f contracts/pom.xml install
```

The JAR is installed under
`.m2/repository/installed/net/aschemann/examples/stubborn-split-contracts`.

## Failing local resolution

The producer binds `generateTests` to the `process-test-resources` phase
in its `generate-contracts` execution. Run that phase:

```sh
mvn -f producer/pom.xml process-test-resources
```

Expected: the plugin loads the installed contracts JAR and generates a test.
Actual: the plugin reports that no stubs or contracts were found.

The example assumes these coordinates have not previously been installed
into the unsplit local repository. An old unsplit copy can mask the error.

## Successful file-repository control

```sh
mvn -f producer/pom.xml -Dcontracts.mode=REMOTE '-DcontractsRepositoryUrl=file:///${user.home}/.m2/repository/installed' process-test-resources
```

This generates one test class from the same JAR. No application code,
broker, private repository or credentials are required.
The lifecycle stops before test compilation and execution.

Maven expands `${user.home}`. The single quotes preserve the expression
for Maven without shell-specific environment variable expansion.
