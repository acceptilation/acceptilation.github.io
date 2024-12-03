# Sonar Setup

Follow these steps to submit coverage reports and source code to SonarQube.

## SonarQube setup

### Add project

1. In SonarQube's top navigation, select "Projects".
2. From the "Create Project" dropdown menu in the upper right, select "GitHub".
3. Search for the target repository, then select "Set up selected repository".

### Set permissions

1. Go to the target project in SonarQube.
2. From the "Project Settings" dropdown menu in the upper right, select "Permissions".
3. Grant team group all permissions to manage the project.
4. Ask another group member to remove your specific user's permissions because you cannot remove yourself.

### Get project key

1. Go to the target project in SonarQube.
2. Select "Project information" in the upper right and note the project key.

[Gradle projects](#apply-gradle-project-key) use this project key in their `build.gradle.kts` file.
[.Net projects](#apply-net-project-key) use this project key as a workflow input.

### Create quality gate

1. Select "Quality Gates" from the top navigation.
2. Select "DEFAULT QUALITY GATE" from the list of quality gates.
3. Select "Copy" in the upper right.
4. Name the new quality gate, e.g. the name of the team.
5. Increase required coverage to 90%.
6. Grant team group permission to manage this quality gate.

### Apply quality gate

1. Go to the target project in SonarQube.
2. From the "Project Settings" dropdown menu in the upper right, select "Quality Gate".
3. Always use a specific Quality Gate: the gate created in the previous step.
4. Select "Save".

## Repository setup

### Apply Gradle project key

In a Gradle project's `build.gradle.kts` file, set `sonar.projectKey` to the key copied from SonarQube:

```kotlin
sonarqube {
    properties {
        property("sonar.projectKey", "project_key_goes_here")
    }
}
```

### Apply .Net project key

In a .Net project's workflows, set `sonar-key` to the key copied from SonarQube:

```yaml
jobs:
  test:
    uses: some-org/pipelines-repo/.github/workflows/dotnet-test.yaml@v1
    with:
      sonar-key: project_key_goes_here
```
