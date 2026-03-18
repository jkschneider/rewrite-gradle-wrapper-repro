# Reproduction: UpdateGradleWrapper reads from disk, not git

This project demonstrates that `org.openrewrite.gradle.UpdateGradleWrapper` reads
from the filesystem working directory, **not** from the git committed state.

## Reproduce

```bash
git clone https://github.com/jkschneider/rewrite-gradle-wrapper-repro.git
cd rewrite-gradle-wrapper-repro

# Committed state is Gradle 8.14.4.
# Downgrade to 8.14.3 on disk without committing:
./gradlew wrapper --gradle-version 8.14.3

# Run the recipe (targets latest 8.x):
./gradlew rewriteRun

# The recipe detects 8.14.3 on disk and upgrades to 8.14.4:
grep distributionUrl gradle/wrapper/gradle-wrapper.properties
```

## What this proves

Despite the git HEAD commit having Gradle `8.14.4`, the recipe correctly sees the
on-disk version (`8.14.3`) and upgrades it. The plugin reads from the working
directory, not from git.

## Note

If your default Java version is too new (e.g., Java 25), you may need to point
to a compatible JDK:

```bash
JAVA_HOME=/path/to/jdk17 ./gradlew rewriteRun
```
