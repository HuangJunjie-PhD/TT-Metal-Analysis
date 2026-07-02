---
project: tutorial-assets
github: https://github.com/tenstorrent/tutorial-assets
deepwiki: https://deepwiki.com/tenstorrent/tutorial-assets/3-repository-infrastructure
---

# Repository Infrastructure

Relevant source files
*   [.gitattributes](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes)
*   [README.md](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/README.md?plain=1)
*   [media/ttnn_visualizer/README.md](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/media/ttnn_visualizer/README.md?plain=1)

This document covers the technical infrastructure and configuration that enables the tutorial-assets repository to function as a centralized storage system for image assets used in TT-NN and TT-Metalium documentation. It explains the Git LFS configuration, repository organization, file management strategies, and integration mechanisms that support the documentation ecosystem.

For information about the actual image assets and their usage in TT-NN Visualizer documentation, see [TT-NN Visualizer Documentation](https://deepwiki.com/tenstorrent/tutorial-assets/2-tt-nn-visualizer-documentation). For licensing terms and usage guidelines, see [Licensing and Usage](https://deepwiki.com/tenstorrent/tutorial-assets/4-licensing-and-usage).

## Git LFS Configuration

The repository uses Git Large File Storage (LFS) to efficiently handle image assets without bloating the Git repository size. The configuration is managed through the `.gitattributes` file.

### LFS File Type Management

**Git LFS File Type Configuration**

The `.gitattributes` file defines which file types are handled by Git LFS:

| File Extension | LFS Configuration | Purpose |
| --- | --- | --- |
| `*.png` | `filter=lfs diff=lfs merge=lfs -text` | PNG image assets |
| `*.jpg` | `filter=lfs diff=lfs merge=lfs -text` | JPEG image assets |

Sources: [.gitattributes 1-2](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes#L1-L2)

### LFS Storage Architecture

**LFS Storage Benefits**

*   Keeps repository clone size minimal by storing only pointer files
*   Enables efficient handling of large binary image files
*   Provides versioning capabilities for image assets
*   Maintains Git workflow compatibility

Sources: [.gitattributes 1-3](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes#L1-L3)

## Repository Structure

The repository follows a hierarchical organization pattern that separates assets by product and feature area.

### Directory Organization

**Repository Structure Components**

| Component | Type | Purpose |
| --- | --- | --- |
| `media/` | Directory | Root container for all media assets |
| `media/ttnn_visualizer/` | Directory | TT-NN Visualizer specific assets |
| `README.md` | Documentation | Repository overview and usage guidelines |
| `media/ttnn_visualizer/README.md` | Documentation | TT-NN Visualizer asset documentation |
| `.gitattributes` | Configuration | Git LFS file type definitions |
| `LICENSE` | Legal | Creative Commons license terms |

Sources: [README.md 1-21](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/README.md?plain=1#L1-L21)[media/ttnn_visualizer/README.md 1-16](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/media/ttnn_visualizer/README.md?plain=1#L1-L16)

## File Management System

The repository implements a structured approach to managing different types of image assets used across the documentation ecosystem.

### Asset Type Classification

**File Management Strategy**

*   **PNG Format**: Preferred for UI screenshots and technical diagrams requiring crisp detail
*   **JPG Format**: Used for photographic content and complex illustrations
*   **Hierarchical Organization**: Assets grouped by product area and feature
*   **Descriptive Naming**: File names indicate content and context

Sources: [.gitattributes 1-2](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes#L1-L2)[media/ttnn_visualizer/README.md 6-9](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/media/ttnn_visualizer/README.md?plain=1#L6-L9)

## Integration Architecture

The repository serves as a centralized asset store that integrates with multiple documentation systems and platforms.

### Documentation Integration Flow

**Integration Points**

| System | Integration Method | Asset Usage |
| --- | --- | --- |
| TT-NN Documentation | Direct asset linking | Technical diagrams, UI screenshots |
| TT-Metalium Documentation | Direct asset linking | Workflow illustrations, concept graphics |
| TT-NN Tutorials | Direct asset linking | Step-by-step screenshots, examples |
| docs.tenstorrent.com | Aggregated publishing | All asset types via documentation systems |

Sources: [README.md 7-16](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/README.md?plain=1#L7-L16)[media/ttnn_visualizer/README.md 3-4](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/media/ttnn_visualizer/README.md?plain=1#L3-L4)

## Version Control Infrastructure

The repository leverages Git's distributed version control with specialized configuration for binary asset management.

### Version Control Components

**Version Control Benefits**

*   **Asset Versioning**: Complete history of image changes and updates
*   **Collaborative Editing**: Multiple contributors can manage assets simultaneously
*   **Change Tracking**: Clear audit trail for asset modifications
*   **Release Management**: Coordinated asset releases with documentation updates

Sources: [.gitattributes 1-3](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes#L1-L3)

## Performance Optimization

The infrastructure is designed to optimize performance for both developers and end-users accessing the documentation.

### Performance Strategy

| Optimization | Implementation | Benefit |
| --- | --- | --- |
| Git LFS | Large file storage outside main repository | Faster clones and pulls |
| Asset Compression | Optimized PNG/JPG encoding | Reduced bandwidth usage |
| CDN Integration | GitHub's content delivery network | Global asset availability |
| Hierarchical Organization | Logical directory structure | Efficient asset discovery |

Sources: [.gitattributes 1-2](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/.gitattributes#L1-L2)[README.md 1-21](https://github.com/tenstorrent/tutorial-assets/blob/eef92772/README.md?plain=1#L1-L21)

Dismiss
Refresh this wiki

Enter email to refresh