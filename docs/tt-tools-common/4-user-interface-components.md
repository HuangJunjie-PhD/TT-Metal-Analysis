---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/4-user-interface-components
---

# User Interface Components

Relevant source files
*   [tt_tools_common/tests/test_widget_app.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/test_widget_app.py)
*   [tt_tools_common/ui_common/common_style.css](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css)
*   [tt_tools_common/ui_common/widgets.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py)

This document covers the reusable UI component library provided by tt-tools-common for building Terminal User Interface (TUI) applications. The library provides custom Textual widgets, modal screens, and a unified CSS theme system that can be integrated into any Tenstorrent command-line tool requiring interactive interfaces.

For detailed API documentation of individual widgets and their methods, see [UI Components API Reference](https://deepwiki.com/tenstorrent/tt-tools-common/7.3-ui-components-api-reference). For guidance on building applications using these components, see [Building Custom TUI Applications](https://deepwiki.com/tenstorrent/tt-tools-common/4.4-building-custom-tui-applications).

## Overview

The UI component library is built on top of the [Textual](https://textual.textualize.io/) framework and provides standardized widgets that implement the Tenstorrent visual design language. All components share a common styling system defined in CSS, enabling consistent appearance across different tools.

The UI system is organized into three primary layers:

| Layer | Purpose | Location |
| --- | --- | --- |
| Widget Classes | Reusable components with built-in behavior | `tt_tools_common/ui_common/widgets.py` |
| CSS Theme | Visual styling and layout rules | `tt_tools_common/ui_common/common_style.css` |
| Integration Examples | Reference implementations | `tt_tools_common/tests/test_widget_app.py` |

**Sources:**[tt_tools_common/ui_common/widgets.py 1-365](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L1-L365)[tt_tools_common/ui_common/common_style.css 1-226](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css#L1-L226)

## Component Architecture

The following diagram illustrates the dependency relationships between UI components and the external Textual framework:

**Widget Inheritance Hierarchy:**

*   **Widget-based components** (`TTHeader`, `TTFooter`): Inherit directly from `textual.widget.Widget` and implement custom rendering using Rich library primitives
*   **Container-based components** (`TTMenu`, `TTCompatibilityMenu`, `TTHostCompatibilityMenu`, `TTDataTable`): Inherit from Textual container types to compose child widgets
*   **Modal screens** (`TTConfirmBox`, `TTHelperMenuBox`, `TTSettingsMenu`): Inherit from `textual.screen.ModalScreen` for overlay dialogs

**Sources:**[tt_tools_common/ui_common/widgets.py 1-19](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L1-L19)[tt_tools_common/tests/test_widget_app.py 8-19](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/test_widget_app.py#L8-L19)

## Component Categories

### Layout Components

Layout components provide structural elements that frame the application interface:

| Component | Purpose | Key Features |
| --- | --- | --- |
| `TTHeader` | Top application header | Auto-updating timestamp, app name/version display |
| `TTFooter` | Bottom application footer | Configurable content, flexible justification |
| `TTDataTable` | Scrollable data grid | Wrapped DataTable with styled headers, cell update methods |

**TTHeader** ([widgets.py 22-45](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L22-L45)) renders a three-column panel displaying version information (left), application name (center), and a live timestamp (right). The timestamp updates every second via `set_interval`.

**TTFooter** ([widgets.py 48-62](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L48-L62)) accepts a list of content strings and arranges them in columns with configurable justification.

**TTDataTable** ([widgets.py 65-117](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L65-L117)) wraps the Textual `DataTable` widget in a `ScrollableContainer` and provides:

*   Pre-styled headers with centering and underlines
*   `update_data()` method for refreshing cell contents without full re-render
*   Automatic row expansion when new data exceeds current row count

**Sources:**[tt_tools_common/ui_common/widgets.py 22-117](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L22-L117)

### Display Components

Display components present read-only information in formatted layouts:

| Component | Purpose | Use Case |
| --- | --- | --- |
| `TTMenu` | Key-value list display | General information panels |
| `TTCompatibilityMenu` | Status list with pass/fail indicators | Feature compatibility checking |
| `TTHostCompatibilityMenu` | Status list with warning notes | Host configuration validation |

All menu components accept an `OrderedDict` or `Dict` to preserve display order. They automatically calculate column widths based on the longest key.

**TTMenu** ([widgets.py 120-146](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L120-L146)) renders each key-value pair with `*` bullet points, yellow bolded keys, and standard text values. Special handling exists for `"Failed to fetch"` keys, which render values in dark orange.

**TTCompatibilityMenu** ([widgets.py 213-243](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L213-L243)) expects values as `(bool, str)` tuples. When the boolean is `False`, the string value renders in dark orange to indicate a warning.

**TTHostCompatibilityMenu** ([widgets.py 149-210](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L149-L210)) accepts values as either strings (fully compatible) or `(str, str)` tuples (current value, recommended value). The border title changes to "Config Warning!" when any tuple values exist, and recommendations display in dark orange with indentation.

**Sources:**[tt_tools_common/ui_common/widgets.py 120-243](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L120-L243)

### Modal Screens

Modal screens provide overlay interfaces for user interactions:

| Component | Purpose | Interaction Pattern |
| --- | --- | --- |
| `TTConfirmBox` | Yes/No confirmation dialog | Button callbacks for yes/no actions |
| `TTHelperMenuBox` | Help text display | Markdown content with keyboard dismissal |
| `TTSettingsMenu` | Application settings panel | Switch widgets for boolean options |

**TTConfirmBox** ([widgets.py 246-271](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L246-L271)) displays a question label with Yes/No buttons in a grid layout. It accepts optional `on_yes` and `on_no` callback functions that execute before the screen automatically pops.

**TTHelperMenuBox** ([widgets.py 274-305](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L274-L305)) renders Markdown content in a modal with built-in bindings for `q`, `escape`, and `h` to dismiss the screen.

**TTSettingsMenu** ([widgets.py 308-364](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/widgets.py#L308-L364)) provides a settings interface specifically designed for tt-smi, with switches for dark mode and software version checking. It demonstrates the `@on(Switch.Changed)` decorator pattern for handling switch events.

**Sources:**[tt_tools_common/ui_common/widgets.py 246-364](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L246-L364)

## Theming System

The theming system uses CSS to define colors, layouts, and visual styles across all components. The stylesheet is located at [tt_tools_common/ui_common/common_style.css](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css)

### Color Palette

The following color variables are defined for consistent branding:

| Variable | Color Value | Usage |
| --- | --- | --- |
| `$red` | #f04f5e | Error states, attention elements |
| `$green` | #1eb57d | Borders, success states, primary text |
| `$yellow` | #ffd10a | Highlights, key text, cursor |
| `$purple` | #786bb0 | Secondary accents |
| `$orange` | darkorange | Warnings, failed states |
| `$gray` | ansi_bright_black | Disabled elements |

**Sources:**[tt_tools_common/ui_common/common_style.css 6-12](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css#L6-L12)

### Global Styles

Global rules ([common_style.css 14-26](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/common_style.css#L14-L26)) apply to all components:

*   Default text color: `$yellow`
*   Scrollbar colors: `$yellow` foreground, `$green` background
*   Border titles: left-aligned, yellow, bold

### Widget-Specific Styles

The stylesheet defines specific rules for each component type:

**Header and Footer** ([common_style.css 28-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/common_style.css#L28-L43)): Fixed height of 3 lines, bold text, green outline, docked to top/bottom respectively.

**DataTable** ([common_style.css 45-85](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/common_style.css#L45-L85)): Green borders and text, centered alignment, yellow cursor with black text, transparent header background, yellow hover states at 60% opacity.

**Menu Widgets** ([common_style.css 87-101](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/common_style.css#L87-L101)): 1-unit padding, green borders and text. All three menu variants (`TTMenu`, `TTCompatibilityMenu`, `TTHostCompatibilityMenu`) share these base styles.

**Modal Screens** ([common_style.css 103-187](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/common_style.css#L103-L187)): Centered alignment, specific sizing for dialogs, green borders on surface background. Buttons use yellow background with white text, changing to red on hover/focus.

**Sources:**[tt_tools_common/ui_common/common_style.css 14-226](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css#L14-L226)

## Component Integration Flow

The following diagram shows how applications integrate UI components at runtime:

**Key Integration Points:**

1.   **CSS Loading**: Applications define `CSS_PATH` as a class attribute pointing to `common_style.css`
2.   **Widget Composition**: The `compose()` method yields widget instances configured with application-specific parameters
3.   **Event Handlers**: Widgets define methods like `on_mount()` to handle lifecycle events
4.   **Rendering**: Each widget implements `render()` returning Rich objects or `compose()` yielding child widgets
5.   **State Updates**: Widgets expose methods like `update_data()` for programmatic content updates

**Sources:**[tt_tools_common/tests/test_widget_app.py 26-88](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/test_widget_app.py#L26-L88)[tt_tools_common/ui_common/widgets.py 30-31](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L30-L31)

## Example Application Structure

The test widget app demonstrates proper component integration:

**Application Structure** ([test_widget_app.py 26-88](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/test_widget_app.py#L26-L88)):

1.   **Class Definition**: `TTApp` inherits from `textual.app.App` with `CSS_PATH` pointing to common_style.css
2.   **Initialization**: Constructor accepts `app_name`, `app_version`, and optional `key_bindings`
3.   **Composition**: `compose()` method arranges widgets in a layout hierarchy using Horizontal/Vertical containers
4.   **Mounting**: `on_mount()` populates the DataTable with initial data (42 rows in the example)
5.   **Actions**: Key bindings trigger action methods like `action_toggle_dark()` and `action_test_confirmbox()`

**Widget ID System**: Each widget instance receives an `id` parameter for later retrieval via `get_widget_by_id()`. This enables programmatic updates like calling `update_data()` on a specific `TTDataTable`.

**Sources:**[tt_tools_common/tests/test_widget_app.py 26-88](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/test_widget_app.py#L26-L88)[tt_tools_common/ui_common/widgets.py 68-75](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L68-L75)

## Usage Patterns

### Basic Widget Instantiation

### DataTable Operations

### Modal Screen Integration

**Sources:**[tt_tools_common/ui_common/widgets.py 22-364](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L22-L364)[tt_tools_common/tests/test_widget_app.py 51-88](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/test_widget_app.py#L51-L88)

## Dependencies

The UI component system requires the following external libraries:

| Library | Version Constraint | Purpose |
| --- | --- | --- |
| textual | >=0.59.0 | Base TUI framework |
| rich | >=13.7.0 | Text rendering and formatting |

These dependencies are declared in `pyproject.toml` and automatically installed when tt-tools-common is installed via pip or as a Debian package.

**Sources:**[tt_tools_common/ui_common/widgets.py 4-16](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L4-L16)

Dismiss
Refresh this wiki

Enter email to refresh