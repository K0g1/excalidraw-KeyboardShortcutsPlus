# KeyboardShortcutsPlus Documentation

KeyboardShortcutsPlus is an advanced scripting extension for the Obsidian Excalidraw plugin. It provides a comprehensive set of keyboard-driven commands for rapid diagramming, precise object manipulation, and automated styling that bypasses the standard UI menus.

## Installation Steps

1.  **Download Script ** :
    *   Download the file `KeyboardShortcutsPlus.md` into the Excalidraw folder of your Obsidian vault 
    *   Paste the provided script code into this file.
2.  **Configure as a Startup Script**:
    *   Go to **Settings** > **Excalidraw**.
    *   Scroll down to the **Automate** section.
    *   In the **Startup script file** field, input the file path for `KeyboardShortcutsPlus.md`, usually Excalidraw/KeyboardShortcutsPlus.md.
3.  **Reload app**:
    *   Restart Obsidian or reload the Excalidraw plugin.
    *   A notification will appear: "KeyboardShortcutsPlus Active."

---

## Command Reference Table

| Category | Action | Mac Shortcut | Windows Shortcut |
| :--- | :--- | :--- | :--- |
| **Movement** | Diagonal Move (1px) | Hold 2 Arrows | Hold 2 Arrows |
| | Fast Diagonal (10px) | Shift + 2 Arrows | Shift + 2 Arrows |
| **Creation** | Blank Clone + Arrow | Cmd + Arrow | Ctrl + Arrow |
| **Sizing** | Adjust Width Only | Shift + < / > | Shift + < / > |
| | Adjust Height Only | Opt + < / > | Alt + < / > |
| | Uniform Scale | Cmd + Shift + < / > | Ctrl + Shift + < / > |
| | Arrow Stroke Width | Cmd + Shift + < / > | Ctrl + Shift + < / > |
| **Text** | Align Left | Cmd + Shift + L | Ctrl + Shift + L |
| | Align Center | Cmd + Shift + ; | Ctrl + Shift + ; |
| | Align Right | Cmd + Shift + ' | Ctrl + Shift + ' |
| | Shrink Shape to Fit | Cmd + / | Ctrl + / |
| **Styling** | Smart Fill Cycle | Opt + F | Alt + F |
| | Color Palette (1-8) | Opt + 1-8 | Alt + 1-8 |
| | Reset Object Style | Cmd + ` | Ctrl + ` |

---

## Detailed Feature Overviews

### 1. Advanced Movement and Navigation
The script implements a background key-state listener that enables 8-axis diagonal movement. Holding two arrow keys simultaneously calculates the diagonal vector, allowing objects to be moved precisely at 45-degree angles. Use the Shift modifier for high-speed adjustments (10px increments).

### 2. High-Speed Diagramming
- **Directional Duplication**: Selecting an object and pressing Cmd/Ctrl + Arrow creates a blank instance of that shape 80 pixels away.
- **Dynamic Connection**: A connecting arrow is automatically generated with "Floating Bindings." These arrows glide along the edges of both shapes dynamically as they are repositioned, maintaining a visual link without manual adjustment.

### 3. Definitive Sizing System
The sizing logic is context-aware based on the selected object type:
- **For Shapes**: Shortcuts isolate the horizontal and vertical axes for discrete stretching/shrinking. Cmd + Shift provides proportional 2D scaling.
- **For Arrows/Lines**: Cmd + Shift + < or > ignores dimensional resizing and instead increases or decreases the stroke width (thickness) of the path.

### 4. Text Layout and Logic
- **Alignment**: Standardizes text positioning within containers (Left, Center, or Right) via a dedicated shortcut set.
- **Metadata Sanitization**: When duplicating text elements, the script automatically removes internal Obsidian Block IDs (`^id_...`) to ensure that duplicated blocks remain clean and do not interfere with vault metadata or display.
- **Auto-Fit**: Recalculates the bounding box of a shape to match its internal text exactly, maintaining a standardized 30-pixel margin.

### 5. Styling and Reset
- **Smart Cycle**: Option/Alt + F cycles fill styles while preserving existing background colors. If an object is currently transparent, it defaults to a pastel yellow before beginning the cycle.
- **Palette Control**: Keys 1-8 apply a pastel palette to shapes and a high-contrast saturated palette to text.
- **Global Reset**: Cmd/Ctrl + ` removes all styling, resets the background to transparent, and returns the stroke and internal text to solid black.
