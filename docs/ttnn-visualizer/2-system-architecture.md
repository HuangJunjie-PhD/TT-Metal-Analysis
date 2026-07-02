---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture
---

# System Architecture

Relevant source files
*   [backend/ttnn_visualizer/app.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py)
*   [backend/ttnn_visualizer/csv_queries.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/csv_queries.py)
*   [backend/ttnn_visualizer/file_uploads.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/file_uploads.py)
*   [backend/ttnn_visualizer/models.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py)
*   [backend/ttnn_visualizer/requirements.txt](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/requirements.txt)
*   [backend/ttnn_visualizer/sessions.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sessions.py)
*   [backend/ttnn_visualizer/settings.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py)
*   [backend/ttnn_visualizer/sftp_operations.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sftp_operations.py)
*   [backend/ttnn_visualizer/utils.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/utils.py)
*   [backend/ttnn_visualizer/views.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py)
*   [package.json](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/package.json)
*   [pyproject.toml](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/pyproject.toml)
*   [scripts/release.mjs](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/scripts/release.mjs)
*   [src/components/Layout.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/Layout.tsx)
*   [src/scss/_layout.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/_layout.scss)

This document provides a comprehensive overview of the TT-NN Visualizer's architectural design. It covers the major components, their relationships, and the data flow through the system. For detailed information about the backend implementation, see [Backend Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2.1-backend-architecture), for frontend specifics, see [Frontend Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2.2-frontend-architecture), and for more about data flow, see [Data Flow and State Management](https://deepwiki.com/tenstorrent/ttnn-visualizer/2.3-data-flow-and-state-management).

## Core Architecture

TT-NN Visualizer is designed as a client-server application with a React frontend and a Flask backend. It supports both local and remote data sources, enabling users to analyze neural network models for performance, memory usage, and operational details.

Sources:

*   `backend/ttnn_visualizer/app.py`
*   `backend/ttnn_visualizer/views.py`
*   `src/components/Layout.tsx`
*   `package.json`

## Component Relationships

The TT-NN Visualizer consists of several interdependent systems working together to provide a seamless user experience for analyzing neural network models.

Sources:

*   `backend/ttnn_visualizer/views.py`
*   `src/components/Layout.tsx`
*   `backend/ttnn_visualizer/queries.py`
*   `backend/ttnn_visualizer/csv_queries.py`
*   `backend/ttnn_visualizer/sftp_operations.py`

## Data Flow Architecture

The data flow in TT-NN Visualizer follows different paths depending on whether the data source is local or remote. This diagram shows the complete data flow from source to visualization.

Sources:

*   `backend/ttnn_visualizer/views.py`
*   `backend/ttnn_visualizer/queries.py`
*   `backend/ttnn_visualizer/csv_queries.py`
*   `backend/ttnn_visualizer/sftp_operations.py`
*   `backend/ttnn_visualizer/serializers.py`
*   `src/components/Layout.tsx:12-34`

## Session Management Architecture

Session management is central to the TT-NN Visualizer, maintaining user context and data source selection across requests.

Sources:

*   `backend/ttnn_visualizer/sessions.py`
*   `backend/ttnn_visualizer/models.py:195-205`
*   `backend/ttnn_visualizer/utils.py:48-103`
*   `src/components/Layout.tsx:31-44`

## Remote Connectivity Architecture

The remote connectivity subsystem allows TT-NN Visualizer to access and analyze data on remote servers.

Sources:

*   `backend/ttnn_visualizer/views.py:782-901`
*   `backend/ttnn_visualizer/sftp_operations.py`
*   `backend/ttnn_visualizer/ssh_client.py`

## Config and Initialization Architecture

The system initializes through a structured setup process that establishes configuration, creates necessary components, and prepares for client requests.

Sources:

*   `backend/ttnn_visualizer/app.py:30-69`
*   `backend/ttnn_visualizer/settings.py:13-122`
*   `backend/ttnn_visualizer/app.py:162-215`

## Technology Stack

The TT-NN Visualizer is built using a modern web technology stack:

| Layer | Technologies | Purpose |
| --- | --- | --- |
| **Frontend** | React, Blueprint, Jotai, React Router | User interface and state management |
| **Visualization** | Plotly.js, vis-network | Charts, graphs, and network visualization |
| **API Communication** | Axios, React Query | HTTP requests and response handling |
| **Backend** | Flask, SQLAlchemy, Paramiko | Server, database access, and SSH connectivity |
| **Data Processing** | Pandas, tt-perf-report | Processing CSV data and performance reports |
| **Deployment** | Gunicorn, Gevent | Web server and async processing |

Sources:

*   `package.json:25-53`
*   `pyproject.toml:8-25`
*   `backend/ttnn_visualizer/requirements.txt`

## Key Design Patterns

The TT-NN Visualizer implements several key design patterns:

1.   **Model-View-Controller (MVC)**: Separation of data models, views (frontend components), and controllers (API routes)
2.   **Repository Pattern**: Encapsulation of data access logic in Query classes
3.   **Singleton Pattern**: Config and database connection management
4.   **Factory Pattern**: Flask app creation via factory function
5.   **Adapter Pattern**: Converting between different data representations (database to JSON)
6.   **Observer Pattern**: Using Jotai for reactive state updates
7.   **Dependency Injection**: Session injection via decorators

Sources:

*   `backend/ttnn_visualizer/app.py:30-69`
*   `backend/ttnn_visualizer/views.py:69-1031`
*   `backend/ttnn_visualizer/decorators.py`
*   `backend/ttnn_visualizer/settings.py:100-121`

This architecture enables the TT-NN Visualizer to efficiently analyze and visualize complex neural network models, with the flexibility to work with both local and remote data sources.

Dismiss
Refresh this wiki

Enter email to refresh