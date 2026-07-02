---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/9-configuration-reference
---

# Configuration Reference

Relevant source files
*   [.env.sample](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/.env.sample)
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
*   [src/libs/blueprintjs/legacySassSvgInlinerFactory.js](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/libs/blueprintjs/legacySassSvgInlinerFactory.js)
*   [vite.config.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/vite.config.ts)

This page documents the configuration options available in the TT-NN Visualizer, including environment variables, application settings, build configuration, and command-line arguments. It serves as a comprehensive reference for customizing and deploying the TT-NN Visualizer.

For information on architecture and components, see [System Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture). For API details, see [API Reference](https://deepwiki.com/tenstorrent/ttnn-visualizer/8-api-reference).

## Environment Variables

The TT-NN Visualizer uses environment variables to configure various aspects of both the frontend and backend. These can be set in a `.env` file at the project root or directly in the system environment.

### Environment Variable Loading

Sources: [backend/ttnn_visualizer/settings.py 7-11](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L7-L11)[backend/ttnn_visualizer/app.py 40-43](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L40-L43)

### Core Environment Variables

| Variable | Default | Description |
| --- | --- | --- |
| `FLASK_ENV` | `development` | Sets the application environment (`development`, `testing`, or `production`) |
| `FLASK_DEBUG` | `false` | Enables Flask debug mode |
| `HOST` | `localhost` | Server host |
| `PORT` | `8000` | Server port |
| `DEV_SERVER_HOST` | `localhost` | Development server host |
| `DEV_SERVER_PORT` | `5173` | Development server port |
| `VITE_API_ROOT` | `http://localhost:8000/api` | Frontend API endpoint |
| `SECRET_KEY` | `90909` | Flask secret key for session security |
| `LAUNCH_BROWSER_ON_START` | `true` | Whether to open a browser when the server starts |
| `DEBUG` | `false` | Debug mode toggle |
| `WEB_CONCURRENCY` | `1` | Number of worker processes |
| `USE_WEBSOCKETS` | `true` | Enable WebSocket support |

Sources: [backend/ttnn_visualizer/settings.py 14-69](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L14-L69)[.env.sample 1-18](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/.env.sample#L1-L18)

### Server Configuration Variables

| Variable | Default | Description |
| --- | --- | --- |
| `GUNICORN_WORKER_CLASS` | `gevent` | Gunicorn worker class |
| `GUNICORN_WORKERS` | `1` | Number of Gunicorn workers |
| `GUNICORN_BIND` | `{HOST}:{PORT}` | Gunicorn bind address |
| `GUNICORN_APP_MODULE` | `ttnn_visualizer.app:create_app()` | Application module for Gunicorn |

Sources: [backend/ttnn_visualizer/settings.py 53-63](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L53-L63)

### Example .env File

```
# Frontend
VITE_API_ROOT=http://localhost:8000/api

# Backend
HOST=localhost
PORT=8000
DEV_SERVER_PORT=5173
DEV_SERVER_HOST=localhost
LAUNCH_BROWSER_ON_START=true
WEB_CONCURRENCY=1
FLASK_ENV=production

# User permissions
UID=1000
GID=1000
```

Sources: [.env.sample 1-18](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/.env.sample#L1-L18)

## Configuration Classes

The TT-NN Visualizer uses a hierarchical configuration system defined in `settings.py`.

Sources: [backend/ttnn_visualizer/settings.py 13-121](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L13-L121)

### Configuration Selection

The application automatically selects the appropriate configuration class based on the `FLASK_ENV` environment variable:

*   `development` (default): Uses `DevelopmentConfig`
*   `testing`: Uses `TestingConfig`
*   `production`: Uses `ProductionConfig`

Configuration values can be overridden by environment variables with matching names.

Sources: [backend/ttnn_visualizer/settings.py 100-121](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L100-L121)

## Directory Structure

The TT-NN Visualizer uses several directories for storing data, reports, and static assets. These paths are configurable.

Sources: [backend/ttnn_visualizer/settings.py 19-29](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L19-L29)

### Path Configuration

| Path Configuration | Default | Description |
| --- | --- | --- |
| `REPORT_DATA_DIRECTORY` | `Path(__file__).parent.absolute().joinpath("data")` | Base directory for report data |
| `LOCAL_DATA_DIRECTORY` | `REPORT_DATA_DIRECTORY.joinpath("local")` | Directory for local data |
| `REMOTE_DATA_DIRECTORY` | `REPORT_DATA_DIRECTORY.joinpath("remote")` | Directory for remote data |
| `PROFILER_DIRECTORY_NAME` | `profiler-reports` | Subdirectory name for profiler reports |
| `PERFORMANCE_DIRECTORY_NAME` | `performance-reports` | Subdirectory name for performance reports |
| `NPE_DIRECTORY_NAME` | `npe-reports` | Subdirectory name for NPE reports |
| `APPLICATION_DIR` | `os.path.abspath(os.path.join(__file__, "..", os.pardir))` | Root application directory |
| `STATIC_ASSETS_DIR` | `Path(APPLICATION_DIR).joinpath("ttnn_visualizer", "static")` | Directory for static assets |

Sources: [backend/ttnn_visualizer/settings.py 19-29](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L19-L29)[backend/ttnn_visualizer/utils.py 17-194](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/utils.py#L17-L194)

## SQLite Database Configuration

The TT-NN Visualizer uses SQLite for storing session data and application state.

Sources: [backend/ttnn_visualizer/settings.py 41-50](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L41-L50)

### Database Settings

| Setting | Default | Description |
| --- | --- | --- |
| `DB_VERSION` | `0.29.0` | Version for database schema |
| `SQLALCHEMY_DATABASE_URI` | `sqlite:///{APPLICATION_DIR}/ttnn_{DB_VERSION}.db` | SQLAlchemy connection URI |
| `SQLALCHEMY_ENGINE_OPTIONS` | `{"pool_size": 10, "max_overflow": 20, "pool_timeout": 30}` | SQLAlchemy engine options |
| `SQLALCHEMY_TRACK_MODIFICATIONS` | `False` | Track modifications flag |

Sources: [backend/ttnn_visualizer/settings.py 41-50](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L41-L50)

## Command-Line Interface

The TT-NN Visualizer provides command-line arguments for specifying data sources when launching the application.

Sources: [backend/ttnn_visualizer/app.py 155-185](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L155-L185)

### Available Arguments

| Argument | Description |
| --- | --- |
| `--profiler-path` | Path to a profiler report directory |
| `--performance-path` | Path to a performance report directory |

Sources: [backend/ttnn_visualizer/app.py 155-159](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L155-L159)

### Usage Examples

`# Launch with a specific profiler reportttnn-visualizer --profiler-path /path/to/profile/report # Launch with specific performance datattnn-visualizer --performance-path /path/to/performance/data # Launch with both profiler and performance datattnn-visualizer --profiler-path /path/to/profile/report --performance-path /path/to/performance/data`
Sources: [backend/ttnn_visualizer/app.py 162-185](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L162-L185)

## Remote Connection Configuration

The TT-NN Visualizer can connect to remote servers to access profiler and performance data.

Sources: [backend/ttnn_visualizer/models.py 166-192](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L166-L192)[backend/ttnn_visualizer/sftp_operations.py 37-485](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sftp_operations.py#L37-L485)

### Remote Connection Settings

| Setting | Description | Required |
| --- | --- | --- |
| `name` | Name of the connection | Yes |
| `username` | SSH username | Yes |
| `host` | Remote hostname or IP address | Yes |
| `port` | SSH port (1-65535) | Yes |
| `profilerPath` | Path to profiler reports on remote server | Yes |
| `performancePath` | Path to performance reports on remote server | No |
| `sqliteBinaryPath` | Path to SQLite binary on remote server | No |
| `useRemoteQuerying` | Whether to use remote querying | No, defaults to `false` |

Sources: [backend/ttnn_visualizer/models.py 166-175](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L166-L175)

### Remote Data Flow

Sources: [backend/ttnn_visualizer/sftp_operations.py 50-486](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sftp_operations.py#L50-L486)[backend/ttnn_visualizer/views.py 695-975](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L695-L975)

## Build Configuration

### Frontend Build Configuration (Vite)

The frontend build configuration is defined in `vite.config.ts` and controlled via environment variables.

Sources: [vite.config.ts 1-59](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/vite.config.ts#L1-L59)

#### Frontend Build Settings

| Setting | Value | Description |
| --- | --- | --- |
| `outDir` | `./backend/ttnn_visualizer/static/` | Output directory for built assets |
| `emptyOutDir` | `true` | Clear output directory before build |
| `APP_VERSION` | From `package.json` | Application version |
| `VITE_API_ROOT` | From environment or `http://localhost:8000/api` | API root URL |
| `proxy` | `/api: 'http://localhost:8000'` | Development server API proxy |

Sources: [vite.config.ts 18-31](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/vite.config.ts#L18-L31)

### Backend Build Configuration

The backend build configuration is defined in `pyproject.toml`.

Sources: [pyproject.toml 1-69](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/pyproject.toml#L1-L69)

#### Python Package Configuration

| Setting | Value | Description |
| --- | --- | --- |
| `name` | `ttnn_visualizer` | Package name |
| `version` | `0.31.0` | Package version |
| `requires-python` | `>=3.10` | Required Python version |
| `package-dir` | `{"": "backend"}` | Package directory mapping |
| `build-system.requires` | `["setuptools==69.5.1", "setuptools-scm==7.1.0", "wheel"]` | Build system requirements |
| `build-system.build-backend` | `setuptools.build_meta` | Build backend |
| `project.scripts` | `{"ttnn-visualizer": "ttnn_visualizer.app:main"}` | Command-line entry points |

Sources: [pyproject.toml 1-46](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/pyproject.toml#L1-L46)

## Version Management

The TT-NN Visualizer manages versions across both frontend and backend components.

Sources: [package.json 1-110](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/package.json#L1-L110)[pyproject.toml 1-69](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/pyproject.toml#L1-L69)[scripts/release.mjs 1-38](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/scripts/release.mjs#L1-L38)

### Version Commands

| Command | Description |
| --- | --- |
| `pnpm run release` | Increment minor version (alias for `release:minor`) |
| `pnpm run release:patch` | Increment patch version (e.g., 0.31.0 → 0.31.1) |
| `pnpm run release:minor` | Increment minor version (e.g., 0.31.0 → 0.32.0) |
| `pnpm run release:major` | Increment major version (e.g., 0.31.0 → 1.0.0) |

The version script updates both `package.json` and `pyproject.toml`, commits the changes, and creates a Git tag.

Sources: [package.json 20-23](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/package.json#L20-L23)[scripts/release.mjs 1-38](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/scripts/release.mjs#L1-L38)

## File Upload Configuration

The TT-NN Visualizer supports uploading profiler reports, performance reports, and NPE (Network Performance Explorer) data.

Sources: [backend/ttnn_visualizer/views.py 592-692](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L592-L692)[backend/ttnn_visualizer/file_uploads.py 1-84](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/file_uploads.py#L1-L84)

### Upload Validation Requirements

| Upload Type | Required Files | Optional Pattern |
| --- | --- | --- |
| Profiler | `db.sqlite`, `config.json` | None |
| Performance | `profile_log_device.csv`, `tracy_profile_log_host.tracy` | Files starting with `ops_perf_results` |
| NPE | Any `.json` file | None |

Sources: [backend/ttnn_visualizer/views.py 592-692](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/views.py#L592-L692)[backend/ttnn_visualizer/file_uploads.py 11-36](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/file_uploads.py#L11-L36)

## Session Management Configuration

The TT-NN Visualizer uses SQLAlchemy for session storage.

Sources: [backend/ttnn_visualizer/models.py 206-284](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/models.py#L206-L284)[backend/ttnn_visualizer/sessions.py 1-309](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/sessions.py#L1-L309)

### Session Configuration Settings

| Setting | Value | Description |
| --- | --- | --- |
| `SESSION_TYPE` | `sqlalchemy` | Session storage type |
| `SESSION_SQLALCHEMY` | `db` | SQLAlchemy instance for session storage |
| `SESSION_COOKIE_SAMESITE` | `Lax` | SameSite cookie policy |
| `SESSION_COOKIE_SECURE` | `False` | Whether cookies require HTTPS |

Sources: [backend/ttnn_visualizer/settings.py 66-67](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/settings.py#L66-L67)[backend/ttnn_visualizer/app.py 88-89](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/backend/ttnn_visualizer/app.py#L88-L89)

## Conclusion

This document provides a comprehensive reference for configuring the TT-NN Visualizer, including environment variables, application settings, build configuration, and command-line arguments. For implementation details, refer to the [System Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture) and [API Reference](https://deepwiki.com/tenstorrent/ttnn-visualizer/8-api-reference) pages.

Dismiss
Refresh this wiki

Enter email to refresh