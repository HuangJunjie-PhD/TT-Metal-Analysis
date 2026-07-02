---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/3-data-source-management
---

# Data Source Management

Relevant source files
*   [src/components/report-selection/AddRemoteConnection.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/AddRemoteConnection.tsx)
*   [src/components/report-selection/ConnectionTestMessage.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/ConnectionTestMessage.tsx)
*   [src/components/report-selection/LocalFolderSelector.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx)
*   [src/components/report-selection/RemoteConnectionDialog.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteConnectionDialog.tsx)
*   [src/components/report-selection/RemoteConnectionSelector.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteConnectionSelector.tsx)
*   [src/components/report-selection/RemoteFolderSelector.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteFolderSelector.tsx)
*   [src/components/report-selection/RemoteSyncConfigurator.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx)
*   [src/definitions/RemoteConnection.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/definitions/RemoteConnection.ts)
*   [src/hooks/useLocal.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useLocal.tsx)
*   [src/hooks/useRemote.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useRemote.tsx)

The Data Source Management system in the TT-NN Visualizer enables loading and managing profiling data from different sources. It provides interfaces and functionality for accessing both local files (uploaded by users) and remote files (via SSH connections), allowing users to analyze memory usage, tensor allocations, and performance metrics from TT-NN model executions.

For information about analyzing the loaded data, see [Operation Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/4-operation-analysis) and [Performance Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/5-performance-analysis).

## Overview of Data Source Management

The TT-NN Visualizer supports two primary data source types:

1.   **Local file system**: Upload and select files directly from your local machine
2.   **Remote servers**: Connect to and sync data from remote servers via SSH

Both source types can provide two categories of data:

*   **Memory reports**: Contain information about memory usage, tensor allocations, and operations
*   **Performance reports**: Contain timing and execution metrics for model operations

### Data Source Architecture

Sources: [src/components/report-selection/LocalFolderSelector.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx)[src/components/report-selection/RemoteSyncConfigurator.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx)[src/hooks/useLocal.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useLocal.tsx)[src/hooks/useRemote.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useRemote.tsx)

## Local Data Management

The local data management system allows users to upload report folders from their local machine and select previously uploaded reports.

### File Requirements

For proper visualization, uploaded folders must contain specific files:

**Memory Reports** require:

*   `db.sqlite`: SQLite database with memory allocation data
*   `config.json`: Configuration information for the report

**Performance Reports** require:

*   `profile_log_device.csv`: Device profiling data
*   `tracy_profile_log_host.tracy`: Host profiling data
*   At least one `ops_perf_results_*` file: Operations performance data

### Local Upload Process

Sources: [src/hooks/useLocal.tsx 93-132](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useLocal.tsx#L93-L132)[src/components/report-selection/LocalFolderSelector.tsx 105-142](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L105-L142)[src/components/report-selection/LocalFolderSelector.tsx 144-179](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L144-L179)

### Selecting Local Reports

After uploading, reports are available for selection via dropdown menus. Selecting a report:

1.   Updates the active report state (`activeProfilerReportAtom` or `activePerformanceReportAtom`)
2.   Sets the report location state to 'local' (`reportLocationAtom`)
3.   Triggers data loading for visualization

Users can also delete previously uploaded reports when they are no longer needed.

Sources: [src/components/report-selection/LocalFolderSelector.tsx 197-235](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L197-L235)

## Remote Data Management

The remote data management system allows connecting to remote servers via SSH to access profiling data without needing to download it manually.

### Remote Connection Management

Connections to remote servers are configured through the `RemoteSyncConfigurator` component, which allows:

*   Adding new connections
*   Editing existing connections
*   Testing connection status
*   Removing connections

Each connection includes:

*   Name: User-defined name for the connection
*   Host: Hostname or IP address
*   Port: SSH port (typically 22)
*   Username: SSH username
*   Memory report folder path: Path to directory containing memory reports
*   Performance report folder path (optional): Path to directory containing performance reports

Sources: [src/components/report-selection/RemoteConnectionDialog.tsx 22-254](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteConnectionDialog.tsx#L22-L254)[src/components/report-selection/RemoteSyncConfigurator.tsx 140-158](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L140-L158)

### Remote Folder Selection and Syncing

Sources: [src/hooks/useRemote.tsx 40-97](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useRemote.tsx#L40-L97)[src/components/report-selection/RemoteSyncConfigurator.tsx 89-108](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L89-L108)[src/components/report-selection/RemoteSyncConfigurator.tsx 191-248](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L191-L248)

The remote folder selection process involves:

1.   Selecting a remote connection
2.   Fetching available folders from the remote server
3.   Selecting a folder to work with
4.   Optionally syncing the folder if it's outdated
5.   Mounting the folder for visualization
6.   Updating the active report state

Remote folders are cached locally and marked with status indicators:

*   Up-to-date (green checkmark): Local copy matches remote
*   Stale (yellow history icon): Remote has been modified since last sync
*   Syncing (spinner): Currently syncing from remote

Sources: [src/components/report-selection/RemoteFolderSelector.tsx 46-92](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteFolderSelector.tsx#L46-L92)

### Persistent Storage

Remote connection information is stored in the browser's local storage for persistence across sessions:

*   Connection configurations
*   Lists of previously synced remote folders
*   Last selected connection and folders

This allows users to quickly reconnect to their commonly used data sources without reconfiguring.

Sources: [src/hooks/useRemote.tsx 99-151](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useRemote.tsx#L99-L151)

## Data Selection State Management

The visualizer uses Jotai atoms to manage data source state:

| Atom | Purpose | Values |
| --- | --- | --- |
| `reportLocationAtom` | Tracks current data source location | 'local' or 'remote' |
| `activeProfilerReportAtom` | Current memory report | Report name or null |
| `activePerformanceReportAtom` | Current performance report | Report name or null |
| `selectedDeviceAtom` | Selected device for visualization | Device ID |
| `fileTransferProgressAtom` | File upload progress tracking | Progress object |

When a new data source is selected:

1.   The appropriate atoms are updated
2.   The query cache is cleared with `queryClient.clear()`
3.   Components re-fetch data based on the new source

Sources: [src/components/report-selection/LocalFolderSelector.tsx 76-78](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L76-L78)[src/components/report-selection/RemoteSyncConfigurator.tsx 31-34](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L31-L34)

## Common Use Cases

### Uploading Local Reports

1.   In the "Memory report" or "Performance report" section, click "Choose directory..."
2.   Select a local directory containing the required report files
3.   The system validates the files and uploads them
4.   Upon successful upload, the report is automatically set as active

The upload process shows progress indicators and validates that necessary files are present before proceeding.

Sources: [src/components/report-selection/LocalFolderSelector.tsx 267-304](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L267-L304)[src/components/report-selection/LocalFolderSelector.tsx 319-352](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L319-L352)

### Setting Up Remote Connections

1.   Click "Add new connection" button
2.   Enter connection details in the dialog: 
    *   Name, host, username, port
    *   Memory report folder path
    *   Optional performance report folder path

3.   Click "Test Connection" to verify connectivity
4.   Click "Add connection" to save the configuration

Connection testing verifies SSH connectivity and folder path accessibility before saving.

Sources: [src/components/report-selection/RemoteConnectionDialog.tsx 96-253](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteConnectionDialog.tsx#L96-L253)

### Syncing Remote Folders

1.   Select a previously configured remote connection
2.   Click "Fetch remote folders list" to retrieve available folders
3.   Select a folder from the dropdown
4.   If the folder status shows it's stale, click the sync button (refresh icon)
5.   Wait for the sync to complete
6.   The report is automatically mounted and set as active

The system maintains timestamps to track when folders were last synced versus when they were last modified on the remote server.

Sources: [src/components/report-selection/RemoteSyncConfigurator.tsx 251-456](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L251-L456)

## Error Handling

The data source management system includes several error handling mechanisms:

*   File validation to ensure required files are present
*   Connection testing before attempting to use remote connections
*   Clear error messages when operations fail
*   Status indicators showing connection and sync states
*   Toast notifications for important state changes

When errors occur during data source operations, appropriate error messages are displayed, and the system remains in a usable state.

Sources: [src/components/report-selection/LocalFolderSelector.tsx 113-116](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderSelector.tsx#L113-L116)[src/components/report-selection/RemoteSyncConfigurator.tsx 241-247](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/RemoteSyncConfigurator.tsx#L241-L247)

Dismiss
Refresh this wiki

Enter email to refresh