# Funnel Builder

A visual, drag-and-drop funnel builder for creating customizable data flow diagrams with calculated metrics between nodes.

## Features

- **Add/Remove Nodes** — Double-click the canvas or use the toolbar button to create nodes. Each node has a label and any number of custom metrics (key, label, value).
- **Pivot-Table CSV Integration** — Upload a CSV file and configure each node to aggregate data using filters and aggregation functions (SUM, AVG, COUNT, etc.). Analyze your data with powerful pivot-table-like capabilities.
- **Drag & Position** — Freely drag nodes on an infinite, pannable canvas. Nodes snap to a grid on release.
- **Connect Nodes** — Click a node's output port (right side) then click another node's input port (left side) or click the node body to draw a connection arrow.
- **Edge Calculations** — Add custom formulas to connections. Expressions reference source and target metrics using `source.<key>` and `target.<key>` syntax. Results are displayed live on the connection label.
- **Pan & Zoom** — Scroll to zoom, drag empty space to pan. Fit-to-view button centers all nodes.
- **Export / Import JSON** — Save your funnel as a `.json` file and reload it later. Auto-saves to browser localStorage.
- **Node Colors** — Pick from 6 color presets to categorize your nodes.

## Usage

1. Open `index.html` in a browser.
2. **Add a node**: Double-click the canvas or click "Add Node" in the toolbar.
3. **Edit a node**: Click a node to select it. The properties panel opens on the right where you can set the label, color, and add/edit metrics.
4. **Connect two nodes**: Click the small circle on the right edge of the source node (output port), then click the circle on the left edge of the target node (input port) or click the target node body directly.
5. **Add calculations to a connection**: Click the connection arrow or its label to select it. In the properties panel, add calculations using expressions like `(target.purchasers / source.lead_count) * 100`.
6. **Export**: Click "Export" to download your funnel as JSON. Click "Import" to load a previously exported file.

## Pivot-Table CSV Integration

The Funnel Builder supports powerful pivot-table-style data analysis using CSV files:

### Getting Started

1. **Upload a CSV**: Click "Upload CSV" in the toolbar and select a CSV file. The first row is treated as headers.
2. **Configure a node**: Click a node to open the properties panel. You'll see three sections: **Data Filters**, **CSV Metrics**, and **Additional Metrics**.

### Data Filters

Filter which rows from your CSV should be included in calculations for this node:

1. Click **"+ Add Filter"** to create a new filter.
2. Select a **column** from your CSV data.
3. Choose an **operator**: Equals, Not Equals, Contains, Greater Than, Less Than.
4. Enter a **value** to filter by (autocomplete suggestions appear from your data).
5. The row count indicator shows how many rows match your filters (e.g., "42 of 150 rows match").

Filters combine with AND logic — all filters must match for a row to be included.

### CSV Metrics

Configure aggregations to compute metrics from your filtered data:

1. Click **"+ Add CSV Metric"** to create a new metric.
2. Select a **column** to aggregate.
3. Choose an **aggregation type**:
   - **SUM**: Sum of all numeric values in the column
   - **AVG**: Average of numeric values
   - **COUNT**: Total number of rows
   - **MIN**: Minimum numeric value
   - **MAX**: Maximum numeric value
   - **COUNT_DISTINCT**: Count of unique values
4. Set a **key** (for use in edge calculations) — auto-generated but editable.
5. Set a **label** (display name on the node) — auto-generated but editable.
6. The **computed value** updates live as you change filters or aggregation settings.

CSV metrics are computed on the client side and appear on your node alongside any manual metrics.

### Example Use Case

**CSV Data:**
```csv
Lead Source,Lead Count,Cost,Revenue
Facebook Ads,1500,3750,12000
Facebook Ads,2200,5500,18000
Google Ads,800,2400,9000
Google Ads,1200,3600,11000
```

**Node Configuration:**
- **Filter**: Lead Source equals "Facebook Ads"
- **Metric 1**: SUM of Lead Count → key: `total_leads`, value: 3700
- **Metric 2**: SUM of Revenue → key: `total_revenue`, value: 30000

Now you can reference `source.total_leads` and `source.total_revenue` in edge calculations!

### CSV Format

Your CSV should have:
- **First row**: Column headers (will be converted to metric keys)
- **Subsequent rows**: Data values

Example:
```csv
Lead Source,Lead Count,Cost Per Lead,Conversion Rate
Facebook Ads,1500,2.50,3.2
Google Ads,2200,3.10,4.1
Email Campaign,800,0.50,5.5
```

### Additional Features

- **Clear CSV**: Click the "×" button next to the CSV indicator in the toolbar to remove the loaded CSV data.
- **Manual Metrics**: You can still add manual metrics in the "Additional Metrics" section — they work alongside CSV metrics.
- **Persistence**: CSV data, filters, and metric configurations are saved to localStorage and included in JSON exports.

## Data Structure (JSON)

```json
{
  "nodes": [
    {
      "id": "node-1",
      "label": "Facebook Ads Funnel",
      "position": { "x": 96, "y": 72 },
      "metrics": [
        { "key": "manual_metric", "label": "Manual Metric", "value": 100 }
      ],
      "color": 0,
      "csvConfig": {
        "filters": [
          { "column": "lead_source", "operator": "equals", "value": "Facebook Ads" }
        ],
        "metrics": [
          { "column": "lead_count", "aggregation": "SUM", "key": "total_leads", "label": "Total Leads" },
          { "column": "revenue", "aggregation": "SUM", "key": "total_revenue", "label": "Total Revenue" }
        ]
      }
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
          "expression": "(target.purchasers / source.total_leads) * 100",
          "unit": "%"
        }
      ]
    }
  ],
  "csvData": {
    "headers": ["lead_source", "lead_count", "revenue"],
    "rawHeaders": ["Lead Source", "Lead Count", "Revenue"],
    "rows": [
      { "lead_source": "Facebook Ads", "lead_count": "1500", "revenue": "12000" },
      { "lead_source": "Google Ads", "lead_count": "800", "revenue": "9000" }
    ],
    "fileName": "marketing_data.csv"
  }
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
