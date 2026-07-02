---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/4-static-site-(eleventy-frontend)
---

# Static Site (Eleventy Frontend)

Relevant source files
*   [.eleventy.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.eleventy.js)
*   [src/_includes/base.njk](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/base.njk)
*   [src/_includes/home.njk](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/home.njk)

The `tt-awesome` website is a high-performance, zero-framework static site built using the **Eleventy (11ty)** static site generator. It serves as the interactive frontend for the project's curated directory, transforming raw JSON data from the `entries/` directory into a responsive, three-pane web application.

The site is designed to feel like a modern web app while remaining entirely static, leveraging Nunjucks for templating and vanilla JavaScript for client-side state management.

### Data Flow Architecture

The frontend follows a unidirectional data pipeline where source JSON is processed by Eleventy filters and injected into Nunjucks templates.

Title: Eleventy Data Pipeline

**Sources:**[.eleventy.js 7-13](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.eleventy.js#L7-L13)[src/_includes/base.njk 78-79](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/base.njk#L78-L79)

* * *

### Three-Pane Layout & State

The core user interface is a three-pane layout that manages visibility via a CSS state machine. The application state (selected category, search query, selected entry) is handled by `main.js` and reflected in the DOM via specific class names on the `.panes` container.

*   **Sidebar (Left):** Navigation and category selection.
*   **List Pane (Middle):** Filtered list of entries based on the active category or search.
*   **Detail Pane (Right):** Full metadata, links, and release history for the selected project.
*   **Releases Pane (Right - Toggle):** A view of recent global ecosystem updates.

For details on the layout implementation and CSS state machine, see **[CSS Layout & Responsive Design (#4.3)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CSS%20Layout%20&%20Responsive%20Design%20(#4.3))**. For details on the JavaScript logic managing these states, see **[Client-Side JavaScript & State Management (#4.4)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/Client-Side%20JavaScript%20&%20State%20Management%20(#4.4))**.

**Sources:**[src/_includes/home.njk 1-46](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/home.njk#L1-L46)[src/assets/main.js 1-50](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/assets/main.js#L1-L50)

* * *

### Template Hierarchy

The site uses a nested Nunjucks template structure. `base.njk` provides the HTML shell, including metadata, SEO tags, and global assets.

| Template | Role |
| --- | --- |
| `base.njk` | Global shell: `<head>`, PostHog, and global JS/CSS injection. |
| `home.njk` | The "Showcase" landing page with the category grid. |
| `sidebar.njk` | Persistent navigation and search input. |
| `entry-list.njk` | The scrollable list of projects for the middle pane. |
| `entry-detail.njk` | The detail card showing project links, hardware, and tags. |

For details on the component structure, see **[Template System & UI Components (#4.2)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/Template%20System%20&%20UI%20Components%20(#4.2))**.

**Sources:**[src/_includes/base.njk 1-81](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/base.njk#L1-L81)[src/_includes/home.njk 24-45](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/home.njk#L24-L45)

* * *

### Data Processing & Diversification

To ensure the homepage remains dynamic and representative, the build process uses a "diversification algorithm" via the `diversifiedFeatured` filter. This logic selects a unique featured project for every category card on the home page, ensuring that a single popular project doesn't dominate multiple categories.

Title: Showcase Selection Logic

For details on custom filters and the diversification logic, see **[Eleventy Configuration & Custom Filters (#4.1)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/Eleventy%20Configuration%20&%20Custom%20Filters%20(#4.1))**.

**Sources:**[.eleventy.js 48-87](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.eleventy.js#L48-L87)

* * *

### Planet Tenstorrent & Feeds

Beyond the project directory, the site includes a **Planet Tenstorrent** sub-site. This is an automated aggregator that merges project updates, research papers, and videos into a single chronological feed.

*   **Planet Sub-site:** Accessible at `/planet/`, it uses `planetItems` filters to group content by month.
*   **Syndication:** The site generates Atom and JSON feeds to allow users to subscribe to new entries or releases.

For details on the aggregator, see **[Planet Tenstorrent Sub-Site (#4.5)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/Planet%20Tenstorrent%20Sub-Site%20(#4.5))**. For details on RSS/Atom/JSON feeds, see **[Syndication Feeds (#4.6)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/Syndication%20Feeds%20(#4.6))**.

**Sources:**[.eleventy.js 89-130](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.eleventy.js#L89-L130)[src/_includes/base.njk 25-28](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/base.njk#L25-L28)

Dismiss
Refresh this wiki

Enter email to refresh