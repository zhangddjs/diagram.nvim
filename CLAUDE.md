# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

diagram.nvim is a Neovim plugin for rendering diagrams inline within text files, powered by image.nvim. It provides a pluggable architecture with **integrations** (file format parsers) and **renderers** (diagram rendering engines).

## Architecture

### Core Components

1. **Main Module** (`lua/diagram/init.lua`): Contains the plugin's main logic, state management, and event handling. Manages the rendering pipeline and buffer lifecycle.

2. **Integration System** (`lua/diagram/integrations/`): 
   - Parses specific file formats to extract diagram code blocks
   - Currently supports Markdown and Neorg
   - Uses TreeSitter queries to parse code fences and extract diagram source

3. **Renderer System** (`lua/diagram/renderers/`):
   - Converts diagram source code to images via external tools
   - Each renderer is responsible for one diagram type (mermaid, plantuml, d2, gnuplot)
   - Handles caching, external process execution, and error handling

4. **Type System** (`lua/diagram/types.lua`): Defines the plugin's type annotations for integrations, renderers, and data structures.

### Key Concepts

- **Diagrams**: Extracted code blocks with metadata (position, renderer, source)
- **Events**: Plugin responds to buffer events (`InsertLeave`, `BufWinEnter`, `TextChanged`, `BufLeave`)
- **Caching**: Each renderer maintains a file-based cache in `~/.cache/nvim/diagram-cache/`
- **Async Rendering**: Uses `vim.fn.jobstart()` for non-blocking external process execution

## Development Commands

This is a Lua-only Neovim plugin with no build system. No linting, testing, or build commands are configured.

## External Dependencies

The plugin requires:
- [image.nvim](https://github.com/3rd/image.nvim) (dependency)
- Terminal with image support (Kitty or Überzug++)
- External rendering tools:
  - `mmdc` (mermaid-cli) for Mermaid diagrams
  - `plantuml` for PlantUML diagrams  
  - `d2` for D2 diagrams
  - `gnuplot` for Gnuplot charts

## Adding New Features

### Adding a New Renderer

1. Create `lua/diagram/renderers/newrenderer.lua` following the `Renderer` interface
2. Implement required fields: `id`, `render(source, options)` function
3. Add to `lua/diagram/renderers/init.lua` exports
4. Update integration files to include the new renderer in their `renderers` array
5. Add default options to `state.renderer_options` in `init.lua`

### Adding a New Integration

1. Create `lua/diagram/integrations/newintegration.lua` following the `Integration` interface
2. Implement: `id`, `filetypes`, `renderers`, `query_buffer_diagrams()` function
3. Add to `lua/diagram/integrations/init.lua` exports  
4. Update default integrations in `init.lua` state

### Key Implementation Details

- Renderers use SHA256 hashing for cache keys based on `renderer.id + ":" + source`
- TreeSitter queries extract diagram blocks from supported file formats
- Image positioning accounts for special cases (neorg requires `diagram_row - 1`)
- Async job completion uses timer polling every 100ms with proper cleanup
- Block quotes in Markdown strip `>` prefixes from diagram source