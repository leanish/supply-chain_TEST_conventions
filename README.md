# Convention consumer fixture

Synthetic consumer covering cases 1, 5, 6, 8 and 9: settings pluginManagement,
plugin-provided test and quality libraries, a Gradle wrapper and pinned Actions.

```sh
./gradlew --no-daemon check
```

Use a Gradle daemon on JDK 21 (the tool runner's default), with JDK 25 installed
for the main build toolchain. CI installs JDK 25. The tests use the convention
plugin's libraries without additional consumer declarations.

[EXPECTED.md](EXPECTED.md) is the single source of case expectations, runnable
acceptance steps, timing caveats and VERIFY items. [VALIDATION.md](VALIDATION.md)
records checks and tool runs already performed. These private repositories are test inputs;
do not publish packages. Claude manages GitHub adoption and the run configuration.
This fixture isn't part of regular tool passes: it runs only in release-candidate
passes that opt fixtures in (`SUPPLY_CHAIN_FIXTURES=1`).
The gate is pinned to supply-chain v0.6.0 and uses the same commit for inventory
and comparison; the tools are at v0.6.0. EXPECTED.md has the pin history.
