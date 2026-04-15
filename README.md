
A high-performance keyboard shortcut extension for Obsidian Excalidraw. Supercharge your workflow with mouse-free diagramming, 8-axis diagonal movement, definitive object sizing, and instant smart-styling.

# KeyboardShortcutsPlus for Obsidian Excalidraw

**KeyboardShortcutsPlus** is an advanced scripting extension that transforms the Obsidian Excalidraw plugin into a keyboard-first diagramming engine. 

Built for power users, this script bypasses standard UI interactions to provide a seamless, uninterrupted workflow. Whether you are building complex mind maps, rapid-fire flowcharts, or wireframes, KeyboardShortcutsPlus allows you to create, connect, resize, and style your elements entirely from the keyboard.

## Why KeyboardShortcutsPlus?
The default Excalidraw interface is highly mouse-dependent. Resizing an object strictly on the X-axis, aligning text, or duplicating shapes with connecting arrows requires multiple clicks and drags. This script intercepts key-states at a low level to map complex graphical manipulations to intuitive keyboard combinations, dramatically accelerating your drafting speed.

## Key Features

* **Rapid Directional Diagramming:** Press a modifier and an arrow key to instantly duplicate a selected shape and automatically link them with a dynamic, self-routing connecting arrow.
* **8-Axis Diagonal Movement:** Features a custom, high-performance background key-listener that detects multiple simultaneous keystrokes. Hold two arrow keys to glide objects diagonally, and use the Shift modifier for 10x high-speed transit. 
* **Definitive Sizing System:** Stop dragging corners. Context-aware shortcuts allow you to isolate horizontal stretching, vertical stretching, or uniform 2D scaling. If a line or arrow is selected, the same shortcuts automatically adapt to adjust stroke thickness.
* **Smart Typography:** Quickly align text (Left, Center, Right) or instantly snap a container's dimensions to perfectly wrap around its internal text with a standardized aesthetic margin.
* **Instant Styling & Resets:** Map your number row to a curated pastel and high-contrast color palette. Cycle through fill styles (Hachure, Cross-hatch, Solid) while preserving your selected colors, or hit the global reset shortcut to instantly strip an object back to a clean, black-and-white wireframe state.
* **Data-Safe Duplication:** Includes internal string sanitization that strips Obsidian Block Reference IDs when duplicating text, preventing vault metadata corruption.

## Compatibility
Fully compatible with both macOS and Windows environments. The script maps directly to physical keyboard hardware codes, ensuring your shortcuts remain consistent regardless of your operating system's software language or regional layout.

## Installation
Please refer to the [Documentation](link-to-docs) for step-by-step instructions on adding this to your Obsidian Vault's Excalidraw Automate startup scripts.
