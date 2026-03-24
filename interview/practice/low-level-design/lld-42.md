**42.** Design a Build System using the Template Method pattern. Steps: Compile, Test, Package, Deploy. Varies by Java/Python/JS.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Build System — Template Method Pattern

**Pattern:** Template Method (Behavioral)
**Why Template Method?** Build pipeline is always Compile -> Test -> Package -> Deploy, but each language has completely different tooling (javac vs pytest vs npm).

**SOLID Principles Applied:**
- **SRP:** Base class owns the pipeline; each language builder owns its tooling
- **OCP:** Add Go, Rust builders without changing base
- **DIP:** CI system depends on abstract BuildPipeline

### Text UML Diagram

```
┌────────────────────────────────┐
│   <<abstract>>                 │
│   BuildPipeline                │
│────────────────────────────────│
│ # build() ← TEMPLATE (final)  │
│────────────────────────────────│
│ # compile()        abstract    │
│ # runTests()       abstract    │
│ # packageArtifact() abstract   │
│ # deploy()         abstract    │
│ # lint()           hook        │  before compile
│ # onBuildSuccess() hook        │
│ # onBuildFailure() hook        │
└──────────┬─────────────────────┘
           │ extends
    ┌──────┼──────┬─────────┐
  Java   Python   JavaScript
 Builder  Builder  Builder
(maven)  (pip/    (npm/
          pytest)  webpack)
```

### Key Design Decisions

1. **Build result tracking** — Each step reports success/failure; pipeline stops on first failure
2. **Lint as hook** — Optional step before compile (e.g., checkstyle for Java, pylint for Python)
3. **Deploy is conditional** — Only deploys if all previous steps pass
4. **Environment config** — Each builder reads language-specific config

### Java Implementation

```java
import java.util.*;

// ============================================================
// BUILD RESULT
// ============================================================

class BuildResult {
    enum Status { SUCCESS, FAILURE, SKIPPED }

    private final Map<String, Status> stepResults = new LinkedHashMap<>();
    private final List<String> logs = new ArrayList<>();
    private long startTime;
    private long endTime;

    public void start() { startTime = System.currentTimeMillis(); }
    public void finish() { endTime = System.currentTimeMillis(); }

    public void addStep(String step, Status status) {
        stepResults.put(step, status);
    }

    public void log(String message) {
        logs.add("[" + System.currentTimeMillis() + "] " + message);
        System.out.println(message);
    }

    public boolean isSuccessful() {
        return stepResults.values().stream().noneMatch(s -> s == Status.FAILURE);
    }

    public long getDurationMs() { return endTime - startTime; }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("\n=== Build Summary ===\n");
        stepResults.forEach((step, status) ->
                sb.append(String.format("  %-15s %s%n", step, status)));
        sb.append(String.format("  Duration: %dms | Overall: %s%n",
                getDurationMs(), isSuccessful() ? "SUCCESS" : "FAILURE"));
        return sb.toString();
    }
}

// ============================================================
// ABSTRACT TEMPLATE
// ============================================================

abstract class BuildPipeline {
    protected BuildResult result;

    /**
     * TEMPLATE METHOD — build pipeline.
     */
    public final BuildResult build(String projectPath) {
        result = new BuildResult();
        result.start();
        result.log("Starting " + getLanguageName() + " build for: " + projectPath);

        try {
            // Step 0: Lint (hook — optional)
            if (shouldLint()) {
                result.log("\n--- Linting ---");
                if (lint()) {
                    result.addStep("Lint", BuildResult.Status.SUCCESS);
                } else {
                    result.addStep("Lint", BuildResult.Status.FAILURE);
                    onBuildFailure("Lint failed");
                    return finishBuild();
                }
            } else {
                result.addStep("Lint", BuildResult.Status.SKIPPED);
            }

            // Step 1: Compile
            result.log("\n--- Compiling ---");
            if (compile(projectPath)) {
                result.addStep("Compile", BuildResult.Status.SUCCESS);
            } else {
                result.addStep("Compile", BuildResult.Status.FAILURE);
                onBuildFailure("Compilation failed");
                return finishBuild();
            }

            // Step 2: Test
            result.log("\n--- Running Tests ---");
            if (runTests()) {
                result.addStep("Test", BuildResult.Status.SUCCESS);
            } else {
                result.addStep("Test", BuildResult.Status.FAILURE);
                onBuildFailure("Tests failed");
                return finishBuild();
            }

            // Step 3: Package
            result.log("\n--- Packaging ---");
            String artifact = packageArtifact();
            result.addStep("Package", BuildResult.Status.SUCCESS);
            result.log("Artifact: " + artifact);

            // Step 4: Deploy
            result.log("\n--- Deploying ---");
            if (deploy(artifact)) {
                result.addStep("Deploy", BuildResult.Status.SUCCESS);
            } else {
                result.addStep("Deploy", BuildResult.Status.FAILURE);
                onBuildFailure("Deployment failed");
                return finishBuild();
            }

            onBuildSuccess();

        } catch (Exception e) {
            result.log("Build error: " + e.getMessage());
            onBuildFailure(e.getMessage());
        }

        return finishBuild();
    }

    private BuildResult finishBuild() {
        result.finish();
        System.out.println(result);
        return result;
    }

    // ---- Abstract methods ----
    protected abstract String getLanguageName();
    protected abstract boolean compile(String projectPath);
    protected abstract boolean runTests();
    protected abstract String packageArtifact();
    protected abstract boolean deploy(String artifact);

    // ---- Hook methods ----
    protected boolean shouldLint() { return false; }
    protected boolean lint() { return true; }

    protected void onBuildSuccess() {
        result.log("Build completed successfully!");
    }

    protected void onBuildFailure(String reason) {
        result.log("BUILD FAILED: " + reason);
    }
}

// ============================================================
// JAVA BUILDER
// ============================================================

class JavaBuildPipeline extends BuildPipeline {
    @Override protected String getLanguageName() { return "Java"; }

    @Override
    protected boolean shouldLint() { return true; }

    @Override
    protected boolean lint() {
        result.log("Running Checkstyle...");
        result.log("Running SpotBugs...");
        return true; // simulate pass
    }

    @Override
    protected boolean compile(String projectPath) {
        result.log("mvn compile -f " + projectPath + "/pom.xml");
        result.log("Compiling 42 source files...");
        return true;
    }

    @Override
    protected boolean runTests() {
        result.log("mvn test");
        result.log("Running 156 tests... 156 passed, 0 failed");
        return true;
    }

    @Override
    protected String packageArtifact() {
        result.log("mvn package -DskipTests");
        return "target/app-1.0.0.jar";
    }

    @Override
    protected boolean deploy(String artifact) {
        result.log("Deploying " + artifact + " to Kubernetes...");
        result.log("kubectl apply -f k8s/deployment.yaml");
        return true;
    }
}

// ============================================================
// PYTHON BUILDER
// ============================================================

class PythonBuildPipeline extends BuildPipeline {
    @Override protected String getLanguageName() { return "Python"; }

    @Override
    protected boolean shouldLint() { return true; }

    @Override
    protected boolean lint() {
        result.log("Running pylint...");
        result.log("Running mypy type checks...");
        return true;
    }

    @Override
    protected boolean compile(String projectPath) {
        // Python is interpreted, but we check syntax
        result.log("python -m py_compile " + projectPath + "/**/*.py");
        result.log("Syntax check passed for 28 files");
        return true;
    }

    @Override
    protected boolean runTests() {
        result.log("pytest --cov=src tests/");
        result.log("89 tests passed, coverage: 87%");
        return true;
    }

    @Override
    protected String packageArtifact() {
        result.log("python setup.py sdist bdist_wheel");
        return "dist/app-1.0.0-py3-none-any.whl";
    }

    @Override
    protected boolean deploy(String artifact) {
        result.log("pip install " + artifact);
        result.log("Deploying via Docker: docker build -t app:latest .");
        return true;
    }
}

// ============================================================
// JAVASCRIPT BUILDER
// ============================================================

class JavaScriptBuildPipeline extends BuildPipeline {
    @Override protected String getLanguageName() { return "JavaScript"; }

    @Override
    protected boolean shouldLint() { return true; }

    @Override
    protected boolean lint() {
        result.log("npx eslint src/");
        return true;
    }

    @Override
    protected boolean compile(String projectPath) {
        result.log("npx tsc --noEmit"); // TypeScript check
        result.log("npx webpack --mode production");
        return true;
    }

    @Override
    protected boolean runTests() {
        result.log("npx jest --coverage");
        result.log("Test Suites: 12 passed | Tests: 78 passed | Coverage: 82%");
        return true;
    }

    @Override
    protected String packageArtifact() {
        result.log("npm pack");
        return "dist/app-1.0.0.tgz";
    }

    @Override
    protected boolean deploy(String artifact) {
        result.log("Deploying to Vercel...");
        result.log("vercel --prod");
        return true;
    }
}

// ============================================================
// CLIENT — CI system
// ============================================================

class CIServer {
    public static void main(String[] args) {
        Map<String, BuildPipeline> pipelines = Map.of(
                "java", new JavaBuildPipeline(),
                "python", new PythonBuildPipeline(),
                "javascript", new JavaScriptBuildPipeline()
        );

        // Build Java project
        BuildResult javaResult = pipelines.get("java").build("/projects/backend-api");

        // Build Python project
        BuildResult pyResult = pipelines.get("python").build("/projects/ml-service");

        // Build JS project
        BuildResult jsResult = pipelines.get("javascript").build("/projects/frontend");
    }
}
```

### Interview Talking Points

- **Real-world CI/CD:** Jenkins, GitHub Actions, GitLab CI all follow this pattern. The pipeline (checkout, build, test, deploy) is fixed; each project fills in language-specific steps.
- **Fail-fast design:** Pipeline stops on first failure — no point packaging if tests fail. This is standard in CI.
- **Hook for lint:** Not all languages need linting in the pipeline. Making it a hook with `shouldLint()` gate follows the NullObject idea — default is skip.
- **Maven/Gradle lifecycle:** Maven's build lifecycle (validate, compile, test, package, verify, install, deploy) is literally a template method implementation.
