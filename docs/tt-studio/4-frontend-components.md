---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/4-frontend-components
---

# Frontend Components

Relevant source files
*   [LICENSE](https://github.com/tenstorrent/tt-studio/blob/a6d347af/LICENSE)
*   [app/frontend/src/components/NavBar.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx)
*   [app/frontend/src/components/SideBar.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx)
*   [app/frontend/src/components/chatui/ChatExamples.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatExamples.tsx)
*   [app/frontend/src/components/ui/file-upload.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/file-upload.tsx)

This document provides a detailed overview of the frontend UI components and their functionality within the TT-Studio application. These components form the user interface that allows users to deploy, manage, and interact with machine learning models on Tenstorrent hardware.

## Component Architecture Overview

TT-Studio frontend is built with React using a modular component-based architecture. The application is structured around specialized interfaces for different AI model types, comprehensive navigation systems, and file handling capabilities.

### Application Root Structure

Sources: [app/frontend/src/components/NavBar.tsx 4-53](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L4-L53)[app/frontend/src/components/SideBar.tsx 12-16](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L12-L16)

The application uses context providers for state management (`ThemeProvider`, `RefreshProvider`, `ModelsProvider`) and React Query for API data fetching. The `HeroSectionProvider` manages the visibility of hero sections across different pages [app/frontend/src/components/NavBar.tsx 53](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L53-L53)

## Navigation and Layout

The navigation system centers around the `NavBar` component, which provides access to all main features. It implements adaptive navigation with three distinct layout modes: vertical sidebar for chat/image generation pages, mobile horizontal layout, and desktop horizontal layout.

*   **NavBar**: Features dynamic item generation based on deployed model types [app/frontend/src/components/NavBar.tsx 46-52](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L46-L52)
*   **SideBar**: A contextual help system that provides page-specific instructions for Home, RAG Management, Chat, and Deployed Models [app/frontend/src/components/SideBar.tsx 21-153](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L21-L153)
*   **Layout Providers**: Manage global UI state such as theme and hero section visibility.

For details, see [Navigation and Layout](https://deepwiki.com/tenstorrent/tt-studio/4.1-navigation-and-layout).

Sources: [app/frontend/src/components/NavBar.tsx 120-191](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L120-L191)[app/frontend/src/components/SideBar.tsx 12-167](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L12-L167)

## Models Deployed Interface

The `ModelsDeployedTable` provides comprehensive model lifecycle management. It allows users to monitor the status of running containers and access their specific interaction UIs.

*   **Status Monitoring**: Displays real-time health badges and container statuses.
*   **Deployment Wizard**: A stepper-based UI for configuring model weights and chip allocation.
*   **Real-time Logs**: Integrated log viewer for debugging model containers.

For details, see [Models Deployed Interface](https://deepwiki.com/tenstorrent/tt-studio/4.2-models-deployed-interface).

Sources: [app/frontend/src/components/NavBar.tsx 44-52](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L44-L52)[app/frontend/src/components/SideBar.tsx 124-150](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L124-L150)

## Chat UI

The chat interface provides a sophisticated conversational experience for LLMs. It handles message streaming, markdown rendering, and specialized "thinking" blocks for reasoning models.

*   **Message Handling**: Supports streaming responses and interaction history.
*   **Input Modalities**: Includes text input, voice recognition, and RAG context selection.
*   **Chat Examples**: A `ChatExamples` component provides rotating predefined prompts to help users get started [app/frontend/src/components/chatui/ChatExamples.tsx 23-64](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatExamples.tsx#L23-L64)

For details, see [Chat UI](https://deepwiki.com/tenstorrent/tt-studio/4.3-chat-ui).

Sources: [app/frontend/src/components/chatui/ChatExamples.tsx 66-150](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatExamples.tsx#L66-L150)[app/frontend/src/components/SideBar.tsx 98-123](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L98-L123)

## Model-Specific Interfaces

Beyond chat, TT-Studio includes specialized UIs for different modalities. The `NavBar` dynamically routes users to these based on the `ModelType` of the deployed container [app/frontend/src/components/NavBar.tsx 46-52](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L46-L52)

*   **Object Detection**: Visualizes bounding boxes on uploaded images.
*   **Image Generation**: Interface for Stable Diffusion prompt engineering.
*   **Speech-to-Text**: Audio recording and transcription pipeline.

For details, see [Model-Specific Interfaces](https://deepwiki.com/tenstorrent/tt-studio/4.4-model-specific-interfaces).

Sources: [app/frontend/src/components/NavBar.tsx 7-24](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L7-L24)

## File Handling and Display

The frontend features robust file management for RAG (Retrieval-Augmented Generation) and model inputs.

*   **FileUpload**: A drag-and-drop component using `react-dropzone` with animated feedback and file metadata display [app/frontend/src/components/ui/file-upload.tsx 30-171](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/file-upload.tsx#L30-L171)
*   **RAG Management**: An interface for uploading documents to create vector datasources [app/frontend/src/components/SideBar.tsx 59-96](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/SideBar.tsx#L59-L96)

For details, see [File Handling and Display](https://deepwiki.com/tenstorrent/tt-studio/4.5-file-handling-and-display).

Sources: [app/frontend/src/components/ui/file-upload.tsx 1-221](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/file-upload.tsx#L1-L221)

## UI Components and Styling

The styling system is built on Tailwind CSS and Framer Motion for high-performance animations.

*   **Shadcn/ui**: Core primitives for dialogs, buttons, and navigation menus [app/frontend/src/components/NavBar.tsx 28-39](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L28-L39)
*   **Theming**: A dual-theme system (Light/Dark) integrated via `DarkModeToggle`[app/frontend/src/components/NavBar.tsx 40](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L40-L40)
*   **Animations**: `AnimatedIcon` and `motion` wrappers provide tactile feedback for user interactions [app/frontend/src/components/NavBar.tsx 94-107](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L94-L107)

For details, see [UI Components and Styling](https://deepwiki.com/tenstorrent/tt-studio/4.6-ui-components-and-styling).

Sources: [app/frontend/src/components/NavBar.tsx 94-107](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/NavBar.tsx#L94-L107)[app/frontend/src/components/ui/file-upload.tsx 9-28](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ui/file-upload.tsx#L9-L28)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh