---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/5-skills-system
---

# CI/CD and Release System

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/CODEOWNERS)
*   [.github/actions/download-artifact/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/download-artifact/action.yaml)
*   [.github/workflows/community-issue-tagging.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/community-issue-tagging.yml)
*   [.github/workflows/download-artifact-test.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/download-artifact-test.yml)
*   [.github/workflows/pr-main.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/pr-main.yml)
*   [.github/workflows/schedule-uplift.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/schedule-uplift.yml)

## Purpose and Scope

This document describes the TT-Forge CI/CD and release system, which automates the complete software delivery lifecycle from daily builds to stable production releases. The system manages four repositories (tt-forge, tt-xla, tt-forge-fe, tt-mlir) through a unified release pipeline that handles artifact building, testing, and publication.

For information about the testing infrastructure that validates releases, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-forge/4-testing-infrastructure). For details about the benchmarking system used to validate performance, see [Benchmarking System](https://deepwiki.com/tenstorrent/tt-forge/3-benchmarking-system).

## System Architecture

The CI/CD system is built on GitHub Actions and consists of three layers: orchestration workflows that coordinate releases across repositories, core release workflows that execute the build-test-publish pipeline, and reusable actions that provide common functionality.

### Orchestration Layer

```mermaid
graph TB
    subgraph "Triggers"
        Cron["Cron Trigger<br/>04:00 UTC daily"]
        Manual["Manual Trigger<br/>workflow_dispatch"]
    end
    
    subgraph "daily-releaser.yml"
        GetRepos["get-repos<br/>Query all managed repositories"]
        GetBranches["get-release-branches<br/>Find branches with new commits"]
        
        NightlyPath["Nightly Path<br/>Build from main branch"]
        RCStablePath["RC/Stable Path<br/>Build from release branches"]
    end
    
    subgraph "Version Management Workflows"
        CreateBranch["create-version-branches.yml<br/>Create release-X.Y branch + RC1"]
        BumpVersion["bump-version.yml<br/>Increment RC or patch version"]
        PromoteStable["promote-stable.yml<br/>Promote RC to stable"]
        UpdateReleases["update-releases.yml<br/>Auto-increment versions"]
    end
    
    Release["release.yml<br/>Core build-test-publish pipeline"]
    
    Cron --> GetRepos
    Manual --> CreateBranch
    Manual --> BumpVersion
    Manual --> PromoteStable
    
    GetRepos --> NightlyPath
    GetRepos --> GetBranches
    GetBranches --> RCStablePath
    
    RCStablePath --> UpdateReleases
    UpdateReleases --> Release
    NightlyPath --> Release
    CreateBranch --> Release
    BumpVersion --> Release
    PromoteStable --> Release
```


**Sources:**[.github/workflows/daily-releaser.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/daily-releaser.yml)[.github/workflows/release.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/release.yml)[.github/workflows/create-version-branches.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/create-version-branches.yml)[.github/workflows/bump-version.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/bump-version.yml)[.github/workflows/promote-stable.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/promote-stable.yml)[.github/workflows/update-releases.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/update-releases.yml)

The `daily-releaser.yml` workflow serves as the central orchestrator, running on a cron schedule at 04:00 UTC. It spawns two parallel paths: the nightly path builds development releases from the main branch of each repository, while the RC/Stable path checks all release branches for new commits and triggers version bumps when detected. Manual workflows provide control points for creating release branches, bumping versions, and promoting releases to stable.

### Core Release Pipeline

```mermaid
graph TB
    subgraph "Build Phase"
        SetFacts["set-release-facts<br/>Determine version, config"]
        CheckExists["Check existing release<br/>Skip if exists"]
        
        subgraph "Artifact Creation"
            UpliftWait["uplift-artifacts or<br/>wait-on-tt-pypi-wheels"]
            BuildWheel["build-release-wheel<br/>Re-version Python packages"]
            BuildDocker["docker-build-push<br/>Create container images"]
            TTForge["tt-forge-wheel<br/>Build meta-package"]
        end
    end
    
    subgraph "Test Phase"
        BasicTests["basic-tests.yml<br/>Smoke tests"]
        DemoTests["demo-tests.yml<br/>Model validation"]
        PerfTests["perf-benchmark.yml<br/>Performance regression"]
    end
    
    subgraph "Publish Phase"
        DocsGen["docs-generator<br/>Changelog + README"]
        PyPI["publish-tenstorrent-pypi<br/>Upload to S3-backed PyPI"]
        DockerTag["Tag latest if stable"]
        GitTag["push-annotated-git-tag<br/>GPG signed"]
        GHRelease["publish-github-release<br/>Create release page"]
    end
    
    SetFacts --> CheckExists
    CheckExists -->|proceed| UpliftWait
    UpliftWait --> BuildWheel
    BuildWheel --> BuildDocker
    BuildWheel --> TTForge
    
    BuildDocker --> BasicTests
    BuildDocker --> DemoTests
    
    BasicTests --> DocsGen
    DemoTests --> DocsGen
    
    DocsGen --> PyPI
    PyPI --> DockerTag
    DockerTag --> GitTag
    GitTag --> GHRelease
    
    GHRelease --> PerfTests
```


**Sources:**[.github/workflows/release.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/release.yml)[.github/actions/set-release-facts/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/set-release-facts/action.yaml)[.github/actions/build-release-wheel/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/build-release-wheel/action.yml)[.github/actions/docker-build-push/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/docker-build-push/action.yml)

The `release.yml` workflow implements a strict build → test → publish sequence with quality gates. The build phase determines configuration through `set-release-facts`, checks for existing releases to avoid duplicates, uplifts or waits for dependency artifacts, and creates versioned Python wheels and Docker images. The test phase runs basic smoke tests and comprehensive demo tests to validate functionality. The publish phase generates documentation, uploads to PyPI, tags Docker images, creates GPG-signed Git tags, and publishes GitHub releases. Performance tests run asynchronously after publication.

## Release Types and Versioning

The system supports four release types with distinct version formats and promotion paths:

| Release Type | Version Format | Example | Source Branch | Prerelease | Latest Tag |
| --- | --- | --- | --- | --- | --- |
| Nightly | `X.Y.Z.devYYYYMMDD` | `0.1.0.dev20250115` | `main` | Yes | No |
| Release Candidate | `X.Y.ZrcN` | `0.1.0rc1` | `release-X.Y` | Yes | No |
| Stable | `X.Y.Z` | `0.1.0` | `release-X.Y` | No | Yes |
| Patch | `X.Y.Z+1` | `0.1.1` | `release-X.Y` | No | Yes |

**Sources:**[.github/actions/set-release-facts/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/set-release-facts/action.yaml)

Version numbers are derived from the `.version` file in the repository root, which contains `MAJOR` and `MINOR` variables. The `set-release-facts` action constructs the full version tag based on the release type input parameter.

### Version Progression State Machine

**Sources:**[.github/workflows/update-releases.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/update-releases.yml)[.github/workflows/promote-stable.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/promote-stable.yml)

The version progression follows a controlled path: nightly builds occur daily from the main branch until a release branch is created. The `create-version-branches.yml` workflow creates the release branch and initial RC1 tag. As new commits are pushed to the release branch, `update-releases.yml` automatically increments the RC number (RC1 → RC2 → RC3). When testing is complete, `promote-stable.yml` removes the RC suffix to create a stable release. Subsequent commits to the stable release branch increment the patch version (0.1.0 → 0.1.1 → 0.1.2).

## Configuration System

The `set-release-facts` action serves as the central configuration authority for the release system. It determines all release parameters based on repository name, release type, and draft status.

### Configuration Resolution Flow

```mermaid
graph TB
    Input["Inputs:<br/>repo, release_type, draft,<br/>new_version_tag, branch"]
    
    ReadVersion["Read .version file<br/>Extract MAJOR, MINOR"]
    
    AllDefaults["Set All Repo Defaults<br/>workflow_allow_failed=false<br/>prerelease=true<br/>make_latest=false"]
    
    TypeDefaults["Apply Release Type Defaults"]
    
    subgraph "Release Type Logic"
        Stable["stable:<br/>prerelease=false<br/>make_latest=true"]
        Nightly["nightly:<br/>new_version_tag=X.Y.Z.devYYYYMMDD<br/>workflow_allow_failed=true<br/>build_release_find_workflow_branch=main"]
        RC["rc:<br/>Use all repo defaults"]
    end
    
    RepoSpecific["Apply Repo-Specific Config"]
    
    subgraph "Repository Configs"
        ForgeFE["tt-forge-fe:<br/>workflow=On nightly<br/>pip_wheel_names=tt_forge_fe tt_tvm<br/>artifact_download_glob=*wheel*"]
        
        MLIR["tt-mlir:<br/>workflow=schedule-nightly.yml<br/>pip_wheel_names=ttmlir<br/>skip_docker_build=true"]
        
        XLA["tt-xla:<br/>workflow=On nightly<br/>pip_wheel_names=pjrt-plugin-tt<br/>test_perf=true<br/>uplift_artifacts_by_commit=true"]
        
        Forge["tt-forge:<br/>workflow=Daily Releaser<br/>pip_wheel_deps_names=pjrt-plugin-tt<br/>skip_model_compatible_table=true"]
    end
    
    DraftOverrides["Apply Draft Mode Overrides<br/>test_demo_filter=bge_m3<br/>test_perf_filter=resnet<br/>basic_tests_runner_filter=n150"]
    
    Outputs["Outputs:<br/>new_version_tag, gh_new_version_tag,<br/>pip_wheel_names, workflow,<br/>artifact_download_glob, prerelease,<br/>make_latest, skip_docker_build,<br/>test_demo_filter, test_perf, etc."]
    
    Input --> ReadVersion
    ReadVersion --> AllDefaults
    AllDefaults --> TypeDefaults
    TypeDefaults --> Stable
    TypeDefaults --> Nightly
    TypeDefaults --> RC
    Stable --> RepoSpecific
    Nightly --> RepoSpecific
    RC --> RepoSpecific
    RepoSpecific --> ForgeFE
    RepoSpecific --> MLIR
    RepoSpecific --> XLA
    RepoSpecific --> Forge
    ForgeFE --> DraftOverrides
    MLIR --> DraftOverrides
    XLA --> DraftOverrides
    Forge --> DraftOverrides
    DraftOverrides --> Outputs
```


**Sources:**[.github/actions/set-release-facts/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/set-release-facts/action.yaml)

The action implements a layered configuration system: it reads the `.version` file to get the major and minor version numbers, applies all-repo defaults, applies release-type-specific defaults based on the `release_type` input, applies repository-specific configuration based on the `repo` input, and finally applies draft mode overrides if `draft=true`. Each layer can override values from previous layers.

## Multi-Repository Artifact Flow

```mermaid
graph TB
    subgraph "Independent Build Repos"
        XLARepo["tenstorrent/tt-xla"]
        MLIRRepo["tenstorrent/tt-mlir"]
        FERepo["tenstorrent/tt-forge-fe"]
    end
    
    subgraph "Build Workflows"
        XLAWorkflow["On push workflow<br/>Builds pjrt-plugin-tt"]
        MLIRWorkflow["schedule-nightly.yml<br/>Builds ttmlir"]
        FEWorkflow["On nightly workflow<br/>Builds tt_forge_fe, tt_tvm"]
    end
    
    subgraph "GitHub Artifacts"
        XLAArtifacts["xla-whl-release artifacts"]
        MLIRArtifacts["ttmlir-wheel artifacts"]
        FEArtifacts["wheel artifacts"]
    end
    
    subgraph "Artifact Uplift"
        UpliftXLA["uplift-artifacts<br/>by commit or run-id"]
        UpliftMLIR["uplift-artifacts<br/>by commit or run-id"]
        UpliftFE["uplift-artifacts<br/>by commit or run-id"]
    end
    
    TTAPI["tt-pypi.eng.aws.tenstorrent.com<br/>S3-backed PyPI server"]
    
    subgraph "tt-forge Meta-package Build"
        ForgeRepo["tenstorrent/tt-forge"]
        WaitScript["wait-on-tt-pypi-wheels.sh<br/>Poll for pjrt-plugin-tt"]
        TemplateSetup["template-setup.py<br/>Inject version + dependency"]
        BuildForge["Build tt-forge wheel<br/>with direct URL dependency"]
    end
    
    XLARepo --> XLAWorkflow
    MLIRRepo --> MLIRWorkflow
    FERepo --> FEWorkflow
    
    XLAWorkflow --> XLAArtifacts
    MLIRWorkflow --> MLIRArtifacts
    FEWorkflow --> FEArtifacts
    
    XLAArtifacts --> UpliftXLA
    MLIRArtifacts --> UpliftMLIR
    FEArtifacts --> UpliftFE
    
    UpliftXLA --> TTAPI
    UpliftMLIR --> TTAPI
    UpliftFE --> TTAPI
    
    TTAPI --> WaitScript
    WaitScript -->|All deps available| TemplateSetup
    TemplateSetup --> BuildForge
    BuildForge --> TTAPI
```


The release system coordinates artifact dependencies across four repositories: tt-xla, tt-mlir, tt-forge-fe, and tt-forge.

**Sources:**[.github/actions/build-release-wheel/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/build-release-wheel/action.yml)[.github/actions/uplift-artifacts/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/uplift-artifacts/action.yml)[.github/actions/tt-forge-wheel/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/tt-forge-wheel/action.yml)

Each independent repository builds its wheels through repository-specific workflows. The `release.yml` workflow calls `build-release-wheel`, which uses `uplift-artifacts` to retrieve artifacts from successful workflow runs. For tt-forge, the build process is more complex because it depends on pjrt-plugin-tt. The `tt-forge-wheel` action executes a wait script that polls the PyPI server until the correct dependency version is available.

## Build Artifact Processing

The build phase transforms artifacts from upstream workflows into versioned release artifacts.

### Artifact Uplift and Download

The system provides a robust mechanism for downloading and extracting artifacts across repositories. The `Download Artifact` action [.github/actions/download-artifact/action.yaml 1-118](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/actions/download-artifact/action.yaml#L1-L118) handles the complexity of retrieving files from GitHub runs, including:

*   Automatic extraction of `tar`, `tar.gz`, and `tar.zst` archives [.github/actions/download-artifact/action.yaml 81-94](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/actions/download-artifact/action.yaml#L81-L94)
*   Configurable retry logic to handle transient network failures [.github/actions/download-artifact/action.yaml 99-116](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/actions/download-artifact/action.yaml#L99-L116)
*   Security checks to ensure downloads stay within the workspace [.github/actions/download-artifact/action.yaml 58-61](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/actions/download-artifact/action.yaml#L58-L61)

### Submodule Management

The repository automates the uplift of internal dependencies. For example, the `Weekly Forge Models Uplift` workflow [.github/workflows/schedule-uplift.yml 1-92](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L1-L92) automatically updates the `third_party/tt_forge_models` submodule [.github/workflows/schedule-uplift.yml 20](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L20-L20) every Saturday. It performs the following:

*   Fetches the latest commit from the models repository [.github/workflows/schedule-uplift.yml 36](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L36-L36)
*   Updates the submodule [.github/workflows/schedule-uplift.yml 42](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L42-L42)
*   Generates a commit log summary of changes [.github/workflows/schedule-uplift.yml 49-55](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L49-L55)
*   Creates and automatically approves/merges a Pull Request [.github/workflows/schedule-uplift.yml 59-89](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/%20.github/workflows/schedule-uplift.yml#L59-L89)

## Documentation and Publication

After artifacts are built and tested, the system generates documentation and publishes through multiple channels.

### Publication Channels

```mermaid
graph TB
    Artifacts["Release Artifacts<br/>Wheels, Docker images, docs"]
    
    subgraph "publish-tenstorrent-pypi"
        AssumeRole["Assume AWS IAM role"]
        UploadS3["Upload wheels to S3 bucket"]
        VerifyIndex["Verify wheels in PyPI index"]
    end
    
    subgraph "push-annotated-git-tag"
        SignTag["Create GPG-signed tag"]
        PushTag["Push tag to repository"]
    end
    
    subgraph "publish-github-release"
        UploadAssets["Upload artifact files"]
        SetMetadata["Set release metadata<br/>prerelease, latest, draft"]
        CreateRelease["Create GitHub release page"]
    end
    
    TTAPI2["tt-pypi.eng.aws.tenstorrent.com<br/>Users: pip install"]
    
    GHCR2["GitHub Container Registry<br/>Users: docker pull"]
    
    GHRelease["GitHub Releases<br/>Users: Download archives"]
    
    Artifacts --> AssumeRole
    AssumeRole --> UploadS3
    UploadS3 --> VerifyIndex
    VerifyIndex --> TTAPI2
    
    Artifacts --> SignTag
    SignTag --> PushTag
    
    Artifacts --> UploadAssets
    UploadAssets --> SetMetadata
    SetMetadata --> CreateRelease
    CreateRelease --> GHRelease
```


**Sources:**[.github/actions/publish-tenstorrent-pypi/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/publish-tenstorrent-pypi/action.yml)[.github/actions/push-annotated-git-tag/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/push-annotated-git-tag/action.yml)[.github/actions/publish-github-release/action.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/publish-github-release/action.yml)

Publication occurs through four channels:

1.   **tt-pypi (S3-backed PyPI)**: Internal repository for wheels.
2.   **Docker Registries**: Images are pushed to GitHub Container Registry (GHCR).
3.   **Git Tags**: GPG-signed tags provide cryptographic verification.
4.   **GitHub Releases**: Final artifacts and release notes are published to the GitHub release page.

## Child Pages

For more technical details on specific components of the release system, refer to the following child pages:

 — Explain the release types (nightly, RC, stable, patch), version progression, and the state machine for release management.
 — Document the `daily-releaser.yml` workflow that orchestrates nightly builds and RC/stable updates across all repositories.
 — Overview of the core `release.yml` workflow and related workflows for creating and managing releases.
 — Overview of the artifact creation process including Python wheels, Docker images, and dependency management.
 — Explain `docs-generator` action, changelog building, installation instructions, and model compatibility tables.
 — Document `test-rc-stable-release-lifecycle.yml` and other workflows that validate the release process.

**Sources:**[.github/workflows/daily-releaser.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/daily-releaser.yml)[.github/workflows/release.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/release.yml)[.github/actions/download-artifact/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/download-artifact/action.yaml)[.github/workflows/schedule-uplift.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/schedule-uplift.yml)[.github/actions/set-release-facts/action.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/actions/set-release-facts/action.yaml)

Dismiss
Refresh this wiki

Enter email to refresh