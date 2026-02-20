# Funnel Builder

A visual, drag-and-drop funnel builder for creating customizable data flow diagrams with calculated metrics between nodes.

## Features

- **Add/Remove Nodes** — Double-click the canvas or use the toolbar button to create nodes. Each node has a label and any number of custom metrics (key, label, value).
- **Drag & Position** — Freely drag nodes on an infinite, pannable canvas. Nodes snap to a grid on release.
- **Connect Nodes** — Click a node's output port (right side) then click another node's input port (left side) to draw a connection arrow.
- **Edge Calculations** — Add custom formulas to connections. Expressions reference source and target metrics using `source.<key>` and `target.<key>` syntax. Results are displayed live on the connection label.
- **Pan & Zoom** — Scroll to zoom, drag empty space to pan. Fit-to-view button centers all nodes.
- **Export / Import JSON** — Save your funnel as a `.json` file and reload it later. Auto-saves to browser localStorage.
- **Node Colors** — Pick from 6 color presets to categorize your nodes.

## Usage

1. Open `index.html` in a browser.
2. **Add a node**: Double-click the canvas or click "Add Node" in the toolbar.
3. **Edit a node**: Click a node to select it. The properties panel opens on the right where you can set the label, color, and add/edit metrics.
4. **Connect two nodes**: Click the small circle on the right edge of the source node (output port), then click the circle on the left edge of the target node (input port).
5. **Add calculations to a connection**: Click the connection arrow or its label to select it. In the properties panel, add calculations using expressions like `(target.purchasers / source.lead_count) * 100`.
6. **Export**: Click "Export" to download your funnel as JSON. Click "Import" to load a previously exported file.

## Data Structure (JSON)

```json
{
  "nodes": [
    {
      "id": "node-1",
      "label": "Lead Source 1",
      "position": { "x": 96, "y": 72 },
      "metrics": [
        { "key": "lead_count", "label": "Lead Count", "value": 1500 }
      ],
      "color": 0
    }
  ],
  "edges": [
    {
      "id": "edge-1",
      "from": "node-1",
      "to": "node-2",
      "calculations": [
        {
          "label": "Conversion Rate",
          "expression": "(target.purchasers / source.lead_count) * 100",
          "unit": "%"
        }
      ]
    }
  ]
}
```

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Double-click canvas | Add new node |
| Delete / Backspace | Delete selected node or edge |
| Escape | Cancel connection / deselect |
| Scroll wheel | Zoom in/out |
| Click + drag (canvas) | Pan |
| Click + drag (node) | Move node |

## Privacy

All data processing happens entirely in the browser. No data is sent to any server.
