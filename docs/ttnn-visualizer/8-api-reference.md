---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/8-api-reference
---

# API Reference

Relevant source files
*   [backend/ttnn_visualizer/app.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py)
*   [backend/ttnn_visualizer/file_uploads.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/file_uploads.py)
*   [backend/ttnn_visualizer/models.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py)
*   [backend/ttnn_visualizer/queries.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/queries.py)
*   [backend/ttnn_visualizer/serializers.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py)
*   [backend/ttnn_visualizer/sessions.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sessions.py)
*   [backend/ttnn_visualizer/settings.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py)
*   [backend/ttnn_visualizer/sftp_operations.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sftp_operations.py)
*   [backend/ttnn_visualizer/tests/__init__.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/tests/__init__.py)
*   [backend/ttnn_visualizer/tests/test_queries.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/tests/test_queries.py)
*   [backend/ttnn_visualizer/tests/test_serializers.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/tests/test_serializers.py)
*   [backend/ttnn_visualizer/utils.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/utils.py)
*   [backend/ttnn_visualizer/views.py](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py)
*   [src/hooks/useAPI.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx)
*   [src/model/APIData.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts)

This page provides a comprehensive reference for the TT-NN Visualizer's API system, including both backend Flask endpoints and frontend React hooks. It serves as the primary technical documentation for developers who need to interact with or extend the visualizer's data access layer.

For general usage information, see [TTNN Visualizer Overview](https://deepwiki.com/tenstorrent/ttnn-visualizer/1-ttnn-visualizer-overview). For information about the frontend architecture, see [Frontend Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2.2-frontend-architecture).

## API Architecture Overview

The TT-NN Visualizer follows a client-server architecture with a Flask backend that exposes REST API endpoints and a React frontend that consumes these endpoints through custom hooks.

Sources:

*   [src/hooks/useAPI.tsx 1-815](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L1-L815)(Complete hooks implementation)
*   [backend/ttnn_visualizer/views.py 1-914](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L1-L914)(API endpoint definitions)
*   [backend/ttnn_visualizer/queries.py 1-389](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/queries.py#L1-L389)(Database query handling)
*   [backend/ttnn_visualizer/sessions.py 1-310](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sessions.py#L1-L310)(Session management)

## Data Flow

The following diagram illustrates how data flows between the frontend hooks and backend endpoints:

Sources:

*   [src/hooks/useAPI.tsx 5-9](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L5-L9)(Import libraries for API communication)
*   [backend/ttnn_visualizer/views.py 67-69](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L67-L69)(API blueprint definition)
*   [backend/ttnn_visualizer/serializers.py 1-258](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L1-L258)(Data serialization)

## Backend API Endpoints

The backend exposes a comprehensive set of RESTful endpoints under the `/api` prefix.

### Session Management

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/session` | GET | Fetch current tab session | None | Session data with active reports and connections |
| `/api/session` | PUT | Update tab session | Session data object | Updated session data |

Sources:

*   [backend/ttnn_visualizer/views.py 53-64](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L53-L64)(Session API endpoints)
*   [backend/ttnn_visualizer/sessions.py 159-196](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sessions.py#L159-L196)(Session update logic)

### Operations

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/operations` | GET | Get list of all operations | None | Array of operation objects |
| `/api/operations/{operation_id}` | GET | Get details for a specific operation | `operation_id` (path), `device_id` (query, optional) | Operation details object |
| `/api/operation-history` | GET | Get operation history | None | Operation history array |

Sources:

*   [backend/ttnn_visualizer/views.py 70-196](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L70-L196)(Operation API endpoints)
*   [backend/ttnn_visualizer/serializers.py 13-64](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L13-L64)(Operation serialization)

### Tensors

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/tensors` | GET | Get list of all tensors | `device_id` (query, optional) | Array of tensor objects |
| `/api/tensors/{tensor_id}` | GET | Get details for a specific tensor | `tensor_id` (path) | Tensor details object |

Sources:

*   [backend/ttnn_visualizer/views.py 221-302](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L221-L302)(Tensor API endpoints)
*   [backend/ttnn_visualizer/serializers.py 230-257](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L230-L257)(Tensor serialization)

### Buffers

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/buffer` | GET | Get buffer by address and operation ID | `address` (query), `operation_id` (query) | Buffer object |
| `/api/buffer-pages` | GET | Get buffer pages | `operation_id` (query), `address` (query, optional), `buffer_type` (query, optional), `device_id` (query, optional) | Array of buffer page objects |
| `/api/buffers` | GET | Get all buffers | `buffer_type` (query, optional), `device_id` (query, optional) | Array of buffer objects |
| `/api/operation-buffers` | GET | Get buffers for all operations | `buffer_type` (query, optional), `device_id` (query, optional) | Array of operation buffer objects |
| `/api/operation-buffers/{operation_id}` | GET | Get buffers for a specific operation | `operation_id` (path), `buffer_type` (query, optional), `device_id` (query, optional) | Operation buffer object |

Sources:

*   [backend/ttnn_visualizer/views.py 236-371](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L236-L371)(Buffer API endpoints)
*   [backend/ttnn_visualizer/serializers.py 102-120](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L102-L120)(Buffer serialization)
*   [backend/ttnn_visualizer/serializers.py 193-227](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L193-L227)(Operation buffer serialization)

### Configuration and Metadata

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/config` | GET | Get configuration data | None | Configuration object |
| `/api/devices` | GET | Get device information | None | Array of device objects |
| `/api/cluster-descriptor` | GET | Get cluster descriptor data | None | Cluster descriptor object |

Sources:

*   [backend/ttnn_visualizer/views.py 199-219](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L199-L219)(Config API endpoint)
*   [backend/ttnn_visualizer/views.py 584-589](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L584-L589)(Devices API endpoint)
*   [backend/ttnn_visualizer/views.py 743-779](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L743-L779)(Cluster descriptor API endpoint)

### Profiler Data

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/profiler` | GET | Get list of profiler data folders | None | Array of profiler folder names |
| `/api/profiler/{profiler_name}` | DELETE | Delete a profiler report | `profiler_name` (path) | Status message |

Sources:

*   [backend/ttnn_visualizer/views.py 374-436](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L374-L436)(Profiler API endpoints)

### Performance Data

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/performance` | GET | Get list of performance data folders | None | Array of performance folder names |
| `/api/performance/{performance_name}` | DELETE | Delete a performance report | `performance_name` (path) | Status message |
| `/api/performance/device-log` | GET | Get device log data | None | Array of device log entries |
| `/api/performance/perf-results` | GET | Get performance results | None | Array of performance results |
| `/api/performance/perf-results/raw` | GET | Get raw performance results | None | CSV content |
| `/api/performance/perf-results/report` | GET | Get performance results report | `name` (query, optional) | Array of performance report entries |
| `/api/performance/device-log/raw` | GET | Get raw device log data | None | CSV content |
| `/api/performance/device-log/zone/{zone}` | GET | Get zone statistics | `zone` (path) | Zone statistics object |

Sources:

*   [backend/ttnn_visualizer/views.py 440-582](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L440-L582)(Performance API endpoints)

### Local File Operations

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/local/upload/profiler` | POST | Upload profiler files | Form data with files | Status message |
| `/api/local/upload/performance` | POST | Upload performance files | Form data with files | Status message |
| `/api/local/upload/npe` | POST | Upload NPE files | Form data with files | Status message |

Sources:

*   [backend/ttnn_visualizer/views.py 592-692](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L592-L692)(Local upload API endpoints)
*   [backend/ttnn_visualizer/file_uploads.py 11-84](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/file_uploads.py#L11-L84)(File validation and upload)

### Remote Operations

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/remote/profiler` | POST | Get remote profiler folders | RemoteConnection object | Array of remote profiler folders |
| `/api/remote/performance` | POST | Get remote performance folders | RemoteConnection object | Array of remote performance folders |
| `/api/remote/test` | POST | Test remote connection | RemoteConnection object | Array of status messages |
| `/api/remote/read` | POST | Read remote file | RemoteConnection object with path | File content |
| `/api/remote/sync` | POST | Sync remote folder | RemoteConnection, folder, and profile objects | Updated folder object |

Sources:

*   [backend/ttnn_visualizer/views.py 695-901](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L695-L901)(Remote operation API endpoints)
*   [backend/ttnn_visualizer/sftp_operations.py 149-486](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sftp_operations.py#L149-L486)(Remote SFTP operations)

### Network Performance Explorer (NPE)

| Endpoint | Method | Description | Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/npe` | GET | Get NPE operation trace data | None | NPE data object |

Sources:

*   [src/hooks/useAPI.tsx 382-385](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L382-L385)(NPE data fetch function)
*   [src/hooks/useAPI.tsx 387-393](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L387-L393)(NPE hook)

## Frontend API Hooks

The frontend offers a set of React hooks that provide access to the backend API endpoints, handling data fetching, caching, and state management.

### Core API Communication

Sources:

*   [src/hooks/useAPI.tsx 5-11](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L5-L11)(Import statements for API libraries)
*   [src/hooks/useAPI.tsx 53-64](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L53-L64)(Basic fetch function examples)

### Operation Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useOperationsList` | None | `{ data, isLoading, error }` | Fetches list of all operations |
| `useOperationDetails` | `operationId: number` | `{ operation, operationDetails }` | Fetches details for a specific operation |
| `usePreviousOperationDetails` | `operationId: number` | Result from `useOperationDetails` | Fetches details for the previous operation |
| `usePreviousOperation` | `operationId: number` | `{ id, name }` or undefined | Gets metadata for the previous operation |
| `useNextOperation` | `operationId: number` | `{ id, name }` or undefined | Gets metadata for the next operation |
| `useOperationListRange` | None | `[min, max]` or null | Gets the ID range for operations |
| `useGetDeviceOperationsListByOp` | None | Array of `{ id, name, ops }` | Gets device operations grouped by operation |
| `useGetDeviceOperationsList` | None | Array of `DeviceOperationMapping` | Gets list of all device operations |

Sources:

*   [src/hooks/useAPI.tsx 81-105](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L81-L105)(fetchOperationDetails)
*   [src/hooks/useAPI.tsx 107-150](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L107-L150)(fetchOperations)
*   [src/hooks/useAPI.tsx 364-379](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L364-L379)(useOperationsList)
*   [src/hooks/useAPI.tsx 395-444](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L395-L444)(useOperationDetails)
*   [src/hooks/useAPI.tsx 446-475](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L446-L475)(Previous/next operation hooks)
*   [src/hooks/useAPI.tsx 477-551](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L477-L551)(Device operation hooks)

### Tensor Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useTensors` | None | `{ data, isLoading, error }` | Fetches list of all tensors |
| `useGetTensorSizesById` | `tensorIdList: number[]` | Array of `{ id, size }` | Gets sizes for specified tensors |

Sources:

*   [src/hooks/useAPI.tsx 638-683](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L638-L683)(fetchTensors and useTensors)
*   [src/hooks/useAPI.tsx 816-831](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L816-L831)(useGetTensorSizesById)

### Buffer Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useGetAllBuffers` | `bufferType: BufferType` | `{ data, isLoading, error }` | Fetches all buffers of a specific type |
| `useBuffers` | `bufferType: BufferType, useRange?: boolean` | `{ data, isLoading, error }` | Fetches buffers with optional range filtering |
| `useBufferPages` | `operationId: number, address?: number, bufferType?: BufferType` | `{ data, isLoading, error }` | Fetches buffer pages |
| `useNextBuffer` | `address: number, consumers: number[], queryKey: string` | `{ data, isLoading, error }` | Fetches the next use of a buffer |
| `useOperationBuffers` | `operationId: number` | `{ data, isLoading, error }` | Fetches buffers for a specific operation |

Sources:

*   [src/hooks/useAPI.tsx 66-79](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L66-L79)(fetchBufferPages)
*   [src/hooks/useAPI.tsx 195-227](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L195-L227)(fetchBuffersByOperation and fetchAllBuffers)
*   [src/hooks/useAPI.tsx 219-225](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L219-L225)(useGetAllBuffers)
*   [src/hooks/useAPI.tsx 256-259](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L256-L259)(fetchOperationBuffers)
*   [src/hooks/useAPI.tsx 261-267](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L261-L267)(useOperationBuffers)
*   [src/hooks/useAPI.tsx 630-636](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L630-L636)(useBufferPages)
*   [src/hooks/useAPI.tsx 693-713](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L693-L713)(fetchNextUseOfBuffer and useNextBuffer)
*   [src/hooks/useAPI.tsx 715-732](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L715-L732)(useBuffers)

### Memory Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useGetL1SmallMarker` | None | `number` | Gets start address of the first L1 small buffer |
| `useGetL1StartMarker` | None | `number` | Gets start of a usable memory region for L1 |

Sources:

*   [src/hooks/useAPI.tsx 227-239](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L227-L239)(useGetL1SmallMarker)
*   [src/hooks/useAPI.tsx 244-253](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L244-L253)(useGetL1StartMarker)

### Performance Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useDeviceLog` | None | `{ data, isLoading, error }` | Fetches device log data |
| `usePerformanceReport` | `name?: string` | `{ data, isLoading, error }` | Fetches performance report |
| `usePerformanceComparisonReport` | `name: string` | `{ data, isLoading, error }` | Fetches performance comparison report |
| `usePerformanceRange` | None | `[min, max]` or null | Gets ID range for performance data |

Sources:

*   [src/hooks/useAPI.tsx 299-305](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L299-L305)(fetchPerformanceReport)
*   [src/hooks/useAPI.tsx 317-344](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L317-L344)(fetchDeviceLogRaw)
*   [src/hooks/useAPI.tsx 608-621](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L608-L621)(usePerformanceRange)
*   [src/hooks/useAPI.tsx 734-742](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L734-L742)(useDeviceLog)
*   [src/hooks/useAPI.tsx 752-770](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L752-L770)(usePerformanceReport)
*   [src/hooks/useAPI.tsx 772-792](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L772-L792)(usePerformanceComparisonReport)

### Metadata Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useReportMeta` | None | `{ data, isLoading, error }` | Fetches report metadata |
| `useDevices` | None | `{ data, isLoading, error }` | Fetches device information |
| `useGetClusterDescription` | None | `{ data, isLoading, error }` | Fetches cluster description |
| `useArchitecture` | `arch: DeviceArchitecture` | `ChipDesign` | Gets architecture information |
| `useNodeType` | `arch: DeviceArchitecture` | `{ cores, dram, eth, pcie }` | Gets node type information |

Sources:

*   [src/hooks/useAPI.tsx 270-274](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L270-L274)(fetchReportMeta)
*   [src/hooks/useAPI.tsx 276-284](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L276-L284)(fetchDevices)
*   [src/hooks/useAPI.tsx 346-349](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L346-L349)(fetchClusterDescription)
*   [src/hooks/useAPI.tsx 351-359](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L351-L359)(useGetClusterDescription)
*   [src/hooks/useAPI.tsx 624-628](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L624-L628)(useReportMeta)
*   [src/hooks/useAPI.tsx 685-691](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L685-L691)(useDevices)
*   [src/hooks/useAPI.tsx 805-813](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L805-L813)(useArchitecture)
*   [src/hooks/useAPI.tsx 832-871](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L832-L871)(useNodeType)

### Session Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useSession` | None | `{ data, isLoading, error }` | Fetches current session data |

Sources:

*   [src/hooks/useAPI.tsx 54-58](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L54-L58)(fetchTabSession)
*   [src/hooks/useAPI.tsx 60-64](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L60-L64)(updateTabSession)
*   [src/hooks/useAPI.tsx 794-804](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L794-L804)(useSession)

### Folder List Hooks

| Hook | Parameters | Returns | Description |
| --- | --- | --- | --- |
| `useReportFolderList` | None | `{ data, isLoading, error }` | Fetches list of report folders |
| `usePerfFolderList` | None | `{ data, isLoading, error }` | Fetches list of performance folders |

Sources:

*   [src/hooks/useAPI.tsx 875-882](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L875-L882)(fetchReportFolderList)
*   [src/hooks/useAPI.tsx 884-892](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L884-L892)(deleteProfiler)
*   [src/hooks/useAPI.tsx 886-892](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L886-L892)(useReportFolderList)
*   [src/hooks/useAPI.tsx 896-899](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L896-L899)(fetchPerfFolderList)
*   [src/hooks/useAPI.tsx 901-905](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L901-L905)(deletePerformance)
*   [src/hooks/useAPI.tsx 907-913](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L907-L913)(usePerfFolderList)

## Key Data Models

The TT-NN Visualizer API uses several core data models for communication between frontend and backend.

### Operation Model

Sources:

*   [src/model/APIData.ts 9-17](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L9-L17)(Operation interface)
*   [src/model/APIData.ts 69-74](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L69-L74)(OperationDetailsData interface)
*   [src/model/APIData.ts 176-183](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L176-L183)(OperationDescription interface)
*   [backend/ttnn_visualizer/models.py 30-35](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L30-L35)(Backend Operation model)

### Tensor Model

Sources:

*   [src/model/APIData.ts 19-51](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L19-L51)(Tensor interface)
*   [backend/ttnn_visualizer/models.py 108-121](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L108-L121)(Backend Tensor model)

### Buffer Models

Sources:

*   [src/model/APIData.ts 53-60](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L53-L60)(BufferData interface)
*   [src/model/APIData.ts 62-67](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L62-L67)(Buffer interface)
*   [src/model/APIData.ts 262-277](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L262-L277)(BufferPage interface)
*   [backend/ttnn_visualizer/models.py 22-28](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L22-L28)(BufferType enum)
*   [backend/ttnn_visualizer/models.py 78-84](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L78-L84)(Backend Buffer model)
*   [backend/ttnn_visualizer/models.py 87-98](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L87-L98)(Backend BufferPage model)

### Device and Session Models

Sources:

*   [src/model/APIData.ts 76-80](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L76-L80)(TabSession interface)
*   [src/hooks/useAPI.tsx 158-177](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L158-L177)(DeviceData interface)
*   [backend/ttnn_visualizer/models.py 38-57](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L38-L57)(Backend Device model)
*   [backend/ttnn_visualizer/models.py 166-175](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L166-L175)(Backend RemoteConnection model)
*   [backend/ttnn_visualizer/models.py 188-193](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L188-L193)(Backend RemoteReportFolder model)
*   [backend/ttnn_visualizer/models.py 195-204](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L195-L204)(Backend Instance model)

## Example Usage

### Frontend Component Example

Here's an example of how to use one of the hooks in a React component:

Sources:

*   [src/hooks/useAPI.tsx 364-379](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L364-L379)(useOperationsList)
*   [src/hooks/useAPI.tsx 395-444](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L395-L444)(useOperationDetails)

### Backend API Request/Response Example

**Request: GET /api/operations**

```
GET /api/operations HTTP/1.1
Host: localhost:8000
Accept: application/json
```

**Response:**

Sources:

*   [backend/ttnn_visualizer/views.py 70-96](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L70-L96)(operation_list endpoint)
*   [backend/ttnn_visualizer/serializers.py 13-64](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/serializers.py#L13-L64)(serialize_operations function)

## Error Handling

The API includes several error handling mechanisms:

1.   **Backend Errors**: Flask routes return appropriate HTTP status codes and error messages
2.   **Frontend Error Handling**: React Query provides error objects and status in the hook results
3.   **Validation**: Request parameters and data models are validated

Common error patterns:

Sources:

*   [src/hooks/useAPI.tsx 81-105](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L81-L105)(fetchOperationDetails error handling)
*   [src/hooks/useAPI.tsx 638-672](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx#L638-L672)(fetchTensors error handling)
*   [backend/ttnn_visualizer/views.py 113-129](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L113-L129)(Backend error handling in operation_detail)
*   [backend/ttnn_visualizer/app.py 113-118](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L113-L118)(Global error handlers)

## API Evolution and Versioning

The TT-NN Visualizer API doesn't currently use explicit versioning, but maintains compatibility through careful changes. The DB schema version is tracked in the configuration:

Any breaking changes to the API would typically be documented in release notes.

Sources:

*   [backend/ttnn_visualizer/settings.py 21](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L21-L21)(DB_VERSION definition)

## Conclusion

This API Reference documents the comprehensive backend endpoints and frontend hooks that make up the TT-NN Visualizer's data access layer. Developers can use this reference to understand how data flows through the system, how to retrieve specific data types, and how to extend the application with new functionality.

For information about the overall architecture, see [System Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture).

Dismiss
Refresh this wiki

Enter email to refresh