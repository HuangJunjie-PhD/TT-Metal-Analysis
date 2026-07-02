---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/6-model-deployment
---

# Model Deployment

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-studio/blob/a6d347af/.gitignore)
*   [.gitmodules](https://github.com/tenstorrent/tt-studio/blob/a6d347af/.gitmodules)
*   [app/frontend/src/App.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/App.tsx)
*   [app/frontend/src/components/DeployModelStep.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx)
*   [app/frontend/src/components/SelectionSteps.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx)
*   [app/frontend/src/components/StepperFooter.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFooter.tsx)
*   [app/frontend/src/components/StepperFormActions.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFormActions.tsx)
*   [app/frontend/src/components/ui/stepper.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/stepper.tsx)

This document covers the model deployment system in TT-Studio, which provides a guided, step-by-step interface for deploying AI models through a web-based stepper workflow. The deployment process handles model selection, hardware allocation (chip selection), and container orchestration for Tenstorrent hardware.

For information about the actual model management and lifecycle operations after deployment, see [Model Management](https://deepwiki.com/tenstorrent/tt-studio/5.2-model-management). For details about the inference execution system, see [Inference System](https://deepwiki.com/tenstorrent/tt-studio/7-inference-system).

## Deployment Architecture Overview

The model deployment system is built around a React stepper component that guides users through a multi-step process. The system integrates with the Docker Control Service to manage container lifecycles and provides real-time feedback via job monitoring.

**Sources:**[app/frontend/src/components/ui/stepper.tsx 1-274](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/stepper.tsx#L1-L274)[app/frontend/src/components/DeployModelStep.tsx 16-62](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L16-L62)[app/frontend/src/components/SelectionSteps.tsx 34-83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L34-L83)[app/frontend/src/components/StepperFooter.tsx 1-70](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFooter.tsx#L1-L70)

## Stepper Component System

The deployment workflow is built on a sophisticated stepper system that manages step navigation, validation, and state persistence. The flow is dynamic based on the detected hardware (e.g., QB2/P300Cx2 vs. single-chip boards).

| Feature | Implementation | File Reference |
| --- | --- | --- |
| Step Navigation | `nextStep()`, `prevStep()`, `resetSteps()` | [app/frontend/src/components/ui/stepper.tsx 58-72](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/stepper.tsx#L58-L72) |
| Step Validation | `isDisabledStep`, `hasCompletedAllSteps` | [app/frontend/src/components/ui/stepper.tsx 103-118](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/stepper.tsx#L103-L118) |
| Form Integration | `StepperFormActions` component | [app/frontend/src/components/StepperFormActions.tsx 12-83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFormActions.tsx#L12-L83) |
| Hardware Logic | `useHardwareConfigStep` logic | [app/frontend/src/components/SelectionSteps.tsx 72-83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L72-L83) |

For details on the specific steps and hardware-aware routing, see [Deployment Workflow](https://deepwiki.com/tenstorrent/tt-studio/6.1-deployment-workflow).

**Sources:**[app/frontend/src/components/ui/stepper.tsx 188-274](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/stepper.tsx#L188-L274)[app/frontend/src/components/SelectionSteps.tsx 72-83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L72-L83)

## Deployment Execution and Monitoring

The `DeployModelStep` component handles the final deployment phase. Unlike previous versions, it now supports asynchronous job monitoring, allowing the user to track progress and view real-time logs without blocking the UI.

### Key Deployment Features:

*   **Asynchronous Polling**: Uses `pollProgress` to check the status of a deployment job every second [app/frontend/src/components/DeployModelStep.tsx 92-144](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L92-L144)
*   **Live Logs**: Users can fetch and view deployment logs directly within the stepper [app/frontend/src/components/DeployModelStep.tsx 64-89](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L64-L89)
*   **Hardware Awareness**: Checks `/docker-api/chip-status/` to prevent deployment if all slots are occupied [app/frontend/src/components/DeployModelStep.tsx 174-197](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L174-L197)
*   **Error Recovery**: Captures and displays detailed error messages from the backend if a deployment fails [app/frontend/src/components/DeployModelStep.tsx 114-126](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L114-L126)

**Sources:**[app/frontend/src/components/DeployModelStep.tsx 92-144](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L92-L144)[app/frontend/src/components/DeployModelStep.tsx 174-197](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/DeployModelStep.tsx#L174-L197)

## Model Configuration and Auto-Deployment

TT-Studio supports complex model configurations, including multi-chip allocation and auto-deployment via URL parameters.

*   **Auto-Deployment**: Triggered by the `auto-deploy` search parameter, allowing external links to initiate model setups automatically [app/frontend/src/components/SelectionSteps.tsx 103-169](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L103-L169)
*   **Chip Configuration**: The `ChipConfigStep` allows users to select specific device IDs or multi-chip modes for models that require them [app/frontend/src/components/SelectionSteps.tsx 13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L13-L13)
*   **Default Weights**: The system currently defaults to standard weights, simplifying the user experience for supported models [app/frontend/src/components/SelectionSteps.tsx 191-196](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L191-L196)

For a full list of configuration options and how to define new model parameters, see [Model Configuration](https://deepwiki.com/tenstorrent/tt-studio/6.2-model-configuration).

**Sources:**[app/frontend/src/components/SelectionSteps.tsx 103-169](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L103-L169)[app/frontend/src/components/SelectionSteps.tsx 191-196](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SelectionSteps.tsx#L191-L196)

## Post-Deployment Management

The `StepperFooter` component appears after successful deployment, providing options for additional deployments and navigation to the deployed models interface.

*   **Deploy Another**: Resets the stepper state via `resetSteps()` and `removeDynamicSteps()`[app/frontend/src/components/StepperFooter.tsx 37-40](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFooter.tsx#L37-L40)
*   **View Deployed Models**: A direct link to `/models-deployed` where users can monitor the running container's health and logs [app/frontend/src/components/StepperFooter.tsx 60-64](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFooter.tsx#L60-L64)

**Sources:**[app/frontend/src/components/StepperFooter.tsx 30-70](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/StepperFooter.tsx#L30-L70)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh