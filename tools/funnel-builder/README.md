# Funnel Builder

A visual, drag-and-drop funnel builder for creating customizable data flow diagrams with calculated metrics between nodes.

## Features

- **Add/Remove Nodes** — Double-click the canvas or use the toolbar button to create nodes. Each node has a label and any number of custom metrics (key, label, value).
- **Duplicate Nodes** — Clone an existing node with all its properties (metrics, color, CSV configuration) using the "Duplicate Node" button in the properties panel or the `Ctrl+D` / `Cmd+D` keyboard shortcut.
- **Pivot-Table CSV Integration** — Upload a CSV file and configure each node to aggregate data using filters and aggregation functions (SUM, AVG, COUNT, etc.). Analyze your data with powerful pivot-table-like capabilities.
- **Multi-Source Derived Metrics** — Create metrics on a node that are computed from metrics of **any or all** upstream (source) nodes. Build true data pipelines where values flow through your funnel. A single expression can combine metrics from multiple source nodes (e.g., `facebook_ads.leads + google_ads.leads`).
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

## Derived Metrics (Multi-Source Data Input)

Derived Metrics allow you to create true data pipelines where metric values flow from upstream (source) nodes to downstream (target) nodes. Unlike edge calculations (which only display results on the connection), derived metrics become **first-class metrics on the target node** — available for display, downstream calculations, and further derivation.

### How It Works

When a node has incoming connections (edges), you can define **Derived Metrics** that compute values based on **any or all** connected source nodes' metrics. The computation follows topological order, so derived metrics can reference other derived metrics in a chain.

### Creating a Derived Metric

1. **Connect nodes**: Create edges from one or more source nodes to your target node.
2. **Select the target node**: Click the target node to open the properties panel.
3. **Add a Derived Metric**: In the "Derived Metrics" section (which appears when the node has incoming edges), click **"+ Add Derived Metric"**.
4. **Configure the metric**:
   - **Key**: A unique identifier for the metric (e.g., `total_leads`).
   - **Label**: Display name (e.g., "Total Leads from All Sources").
   - **Expression**: Formula referencing metrics from any connected source node using `<node_label>.<metric_key>` syntax.
   - **Computed Value**: Shows the live result (read-only).

### Expression Syntax

Derived metric expressions reference source nodes using **sanitized versions of their labels**:

| Syntax | Meaning |
|--------|---------|
| `facebook_ads.lead_count` | Metric `lead_count` from the source node labeled "Facebook Ads" |
| `google_ads.revenue` | Metric `revenue` from the source node labeled "Google Ads" |
| `source.lead_count` | **Shorthand** — only works when there's exactly **one** incoming source node |

**Label Sanitization Rules:**
- Node labels are converted to lowercase
- Non-alphanumeric characters are replaced with underscores (`_`)
- Multiple consecutive underscores are collapsed
- Leading/trailing underscores are removed
- Example: "Facebook Ads (2023)" → `facebook_ads_2023`

**Label Collisions:**
If two source nodes have the same label, they're automatically disambiguated with suffixes: `facebook_ads`, `facebook_ads_2`, etc.

### Variable Hints

When editing a derived metric, the properties panel shows **all available metrics** from **all connected source nodes**, grouped by node. The hints display the exact syntax to use in your expression (e.g., `facebook_ads.lead_count`).

### Example 1: Single-Source Pipeline (using `source.` shorthand)

```
Node A (Manual Metric)
├─ lead_count = 1000

    ↓ [Edge A→B]

Node B (Derived Metric from A)
├─ converted = source.lead_count * 0.05 → 50

    ↓ [Edge B→C]

Node C (Derived Metric from B)
├─ revenue = source.converted * 100 → 5000
```

In this example:
- Node B uses the `source.` shorthand because it has only one incoming edge
- Node C computes `revenue` from Node B's `converted` (which is itself derived)
- All metrics are available for edge calculations and further derivations

### Example 2: Multi-Source Aggregation

```
Facebook Ads (CSV Metrics)
├─ lead_count = 3700
├─ cost = 9250
    ↓

Google Ads (CSV Metrics)
├─ lead_count = 2000
├─ cost = 6000
    ↓         ↓

    Combined Funnel (Derived Metrics)
    ├─ total_leads = facebook_ads.lead_count + google_ads.lead_count → 5700
    ├─ total_cost = facebook_ads.cost + google_ads.cost → 15250
    ├─ cost_per_lead = total_cost / total_leads → 2.68
```

In this example:
- Node "Combined Funnel" receives edges from both "Facebook Ads" and "Google Ads"
- The expression `facebook_ads.lead_count + google_ads.lead_count` references both source nodes
- Internal derived metrics like `total_leads` can be referenced in subsequent derived metrics like `cost_per_lead`

### Example 3: Complex Multi-Source Formula

```
Node A               Node B
├─ revenue = 30000   ├─ cost = 15250
    ↓                     ↓

        Node C (Derived Metrics)
        ├─ profit = node_a.revenue - node_b.cost → 14750
        ├─ roi = (profit / node_b.cost) * 100 → 96.72
```

This demonstrates:
- Referencing different metrics from different source nodes
- Using derived metrics in subsequent derived metric expressions

### Advanced Features

- **Multi-Source Expressions**: A single derived metric expression can reference metrics from **any or all** connected upstream nodes.
- **Mixed Metric Types**: Derived metrics, CSV metrics, and manual metrics all coexist on the same node.
- **Cycle Detection**: If a circular dependency is detected (A → B → A), the tool prevents infinite loops and shows a warning.
- **Disconnection Warnings**: If an edge is deleted and a derived metric still references the disconnected node, a warning badge appears showing which references are broken. The metric is not auto-deleted (you can reconnect it later or update the expression).
- **Topological Computation**: Metrics are computed in dependency order automatically, so complex chains always resolve correctly.
- **Backward Compatibility**: Old saved funnels using the previous single-source format are automatically migrated to the new multi-source syntax on load.

### Important Notes

- **Node Renaming**: If you rename a source node, derived metric expressions are **not** automatically updated. You'll need to manually update any expressions that reference the renamed node.
- **Expression Validation**: If a referenced node label doesn't match any connected source, the expression evaluates that reference as `0`.

### Use Cases

- **Conversion Funnels**: Compute conversion rates at each stage based on the previous stage's output.
- **Cost Attribution**: Calculate cost-per-acquisition by dividing costs from one node by conversions from another.
- **Revenue Modeling**: Build multi-step revenue calculations that flow through your funnel.
- **Dynamic Reporting**: Adjust metrics at the top of the funnel and watch all downstream values update automatically.

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
      "derivedMetrics": [],
      "csvConfig": {
        "filters": [
          { "column": "lead_source", "operator": "equals", "value": "Facebook Ads" }
        ],
        "metrics": [
          { "column": "lead_count", "aggregation": "SUM", "key": "total_leads", "label": "Total Leads" },
          { "column": "revenue", "aggregation": "SUM", "key": "total_revenue", "label": "Total Revenue" }
        ]
      }
    },
    {
      "id": "node-2",
      "label": "Converted Leads",
      "position": { "x": 400, "y": 72 },
      "metrics": [
        { "key": "manual_metric", "label": "Manual Metric", "value": 50 }
      ],
      "color": 0,
      "derivedMetrics": [
        {
          "key": "converted",
          "label": "Converted Leads",
          "expression": "facebook_ads_funnel.total_leads * 0.05"
        }
      ]
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
| Ctrl+D / Cmd+D | Duplicate selected node |
| Delete / Backspace | Delete selected node or edge |
| Escape | Cancel connection / deselect |
| Scroll wheel | Zoom in/out |
| Click + drag (canvas) | Pan |
| Click + drag (node) | Move node |

## Privacy

All data processing happens entirely in the browser. No data is sent to any server.
