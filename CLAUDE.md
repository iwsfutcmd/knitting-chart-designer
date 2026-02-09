# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a knitting chart designer web application - a single-page, self-contained HTML file (`index.html`) that allows users to create and edit knitting patterns on a color grid.

## Running the Application

Open `index.html` directly in a web browser:
```bash
open index.html
```

No build process, dependencies, or server required.

## Architecture

The application is structured as a monolithic HTML file containing:
- **CSS**: Inline styles for the UI components
- **HTML**: Application layout and structure
- **JavaScript**: All application logic in a single `<script>` tag

### Key State Management

The application maintains several global state variables:
- `gridData`: 2D array storing the current color of each cell (hex color strings)
- `history`: Array of grid states for undo/redo functionality (limited to 50 states)
- `currentHistoryIndex`: Current position in history array
- `selectedColor`: Currently selected color from palette
- `isDrawing`: Boolean tracking if user is currently dragging to paint
- `hasDrawn`: Boolean tracking if any cells changed during current drag operation

### Drawing Behavior

The painting logic has nuanced behavior via the `paintCell()` function:
- **Single click with `allowToggle=true`**: If clicking a cell that already has the selected color, it reverts to white (#FFFFFF). Otherwise, applies selected color.
- **Drag painting with `allowToggle=false`**: Always applies the selected color without toggling, even on cells that already have that color.

This distinction is critical: the `mousedown` event passes `allowToggle=true`, while `mouseenter` during drag passes `allowToggle=false`.

### History Management

- Each painting operation saves state only once per action (single click or entire drag operation)
- During drag operations, `shouldSaveState=false` is passed to `paintCell()` to defer history saving until `mouseup`
- The `mouseup` listener saves state only if `hasDrawn=true`, preventing empty history entries
- History is automatically pruned to 50 states to prevent memory issues

## Code Modification Guidelines

When modifying this application:

1. **Maintain the toggle behavior distinction**: Single clicks should toggle, drag operations should not.

2. **Preserve history efficiency**: Only save one history state per user action, not per cell painted.

3. **Keep grid generation dynamic**: The grid is completely regenerated on "Create Grid" - event listeners are bound during creation in `createGrid()`.

4. **Color palette**: The `colors` array defines available colors. White (#FFFFFF) is the "blank" state and the first color in the palette.

5. **Self-contained file**: All code must remain in `index.html` - no external dependencies, scripts, or stylesheets.
