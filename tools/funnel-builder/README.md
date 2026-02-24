# Funnel Builder

A visual, drag-and-drop funnel builder for creating customizable data flow diagrams with calculated metrics between nodes.

## Features

- **Add/Remove Nodes** — Double-click the canvas or use the toolbar button to create nodes. Each node has a label and any number of custom metrics (key, label, value).
- **Duplicate Nodes** — Clone an existing node with all its properties (metrics, color, CSV configuration) using the "Duplicate Node" button in the properties panel or the `Ctrl+D` / `Cmd+D` keyboard shortcut.
- **Undo / Redo** — Full history tracking with 50-level undo stack. Reverse any change with `Cmd+Z` (undo) or `Cmd+Shift+Z` (redo). Works for all operations: add, delete, move, edit, paste, import, and more. Property panel edits are debounced to avoid cluttering history with every keystroke.
- **Pivot-Table CSV Integration** — Upload a CSV file and configure each node to aggregate data using filters and aggregation functions (SUM, AVG, COUNT, etc.). Analyze your data with powerful pivot-table-like capabilities.
- **Global Filter Nodes** — Apply scenario-wide filters that affect all CSV data across every node on the canvas. Set conditions like "Country = US" or "Date > 2024-01-01" once, and all nodes automatically use the filtered dataset. Multiple global filters combine with AND logic.
- **Stream Filter Nodes** — Apply flow-based filters that only affect downstream connected nodes. Unlike global filters (which affect all nodes), stream filters create scoped filtering pipelines where different branches of your funnel can have different filter contexts.
- **Multi-Source Derived Metrics** — Create metrics on a node that are computed from metrics of **any or all** upstream (source) nodes, or even the node's **own metrics** (self-referencing). Build true data pipelines where values flow through your funnel. A single expression can combine metrics from multiple source nodes (e.g., `facebook_ads.leads + google_ads.leads`) or calculate internal fields (e.g., `self.sum_revenue / self.count_orders`).
- **Drag & Position** — Freely drag nodes on an infinite, pannable canvas. Nodes snap to a grid on release.
- **Connect Nodes** — Click a node's output port (right side) then click another node's input port (left side) or click the node body to draw a connection arrow.
- **Edge Calculations** — Add custom formulas to connections. Expressions reference source and target metrics using `source.<key>` and `target.<key>` syntax. Results are displayed live on the connection label.
- **Multi-Select** — Select multiple nodes, text boxes, and edges at once using Shift+Click or box selection (drag on canvas). Move, copy, paste, duplicate, or delete multiple objects simultaneously.
- **Pan & Zoom** — 2-finger swipe (or scroll) to pan, pinch (or Ctrl+scroll) to zoom smoothly. Hold Spacebar + drag or Middle-Click + drag to pan. Fit-to-view button centers all nodes.
- **Export / Import JSON** — Save your funnel as a `.json` file and reload it later. Auto-saves to browser localStorage.
- **Node Colors** — Pick from 6 color presets to categorize your nodes.
- **Text Box Labels** — Add free-form text on the canvas to annotate your funnels. Customize text size, bold, and italic styling.

## Multi-Select

The Funnel Builder supports powerful multi-select capabilities that let you work with multiple objects simultaneously:

### Selecting Multiple Objects

**Shift+Click Selection:**
- Hold `Shift` and click individual objects to toggle them in/out of the selection.
- Click without `Shift` on a selected object to keep it selected (useful for dragging multiple items).
- Click without `Shift` on an unselected object to clear the selection and select only that object.

**Box Selection (Drag-Select):**
- Click and drag on the empty canvas to draw a selection box.
- All nodes and text boxes that intersect with the box will be selected.
- Hold `Shift` while box-selecting to **add** to the existing selection instead of replacing it.

### Multi-Object Operations

Once you have multiple objects selected:

- **Move Together**: Drag any selected object to move all selected objects by the same distance.
- **Copy (`Cmd+C` / `Ctrl+C`)**: Copy all selected objects to the clipboard.
- **Paste (`Cmd+V` / `Ctrl+V`)**: Paste copied objects with new IDs, offset by 40px from the originals.
- **Duplicate (`Cmd+D` / `Ctrl+D`)**: Instantly duplicate all selected objects (copy + paste in one action).
- **Delete (`Delete` / `Backspace`)**: Remove all selected objects and their associated edges at once.

### Canvas Panning vs. Selection

To avoid conflicts between panning and box selection:

- **Spacebar + Drag**: Hold the spacebar and drag to pan the canvas.
- **Middle-Click + Drag**: Use middle mouse button to pan the canvas.
- **Left-Click Drag (on empty space)**: Creates a selection box.

### Properties Panel

When multiple objects are selected, the properties panel shows:
- The number of selected objects
- Available multi-select actions and keyboard shortcuts
- Helpful hints for working with multiple objects

Select a single object to edit its individual properties.

### Use Cases

- **Bulk Organization**: Select and move entire sections of your funnel at once.
- **Template Duplication**: Select a group of related nodes, duplicate them, and create a parallel pipeline.
- **Quick Cleanup**: Select multiple outdated nodes and delete them in one action.
- **Batch Copy-Paste**: Copy a complex funnel structure and paste it to create variations.

## Undo / Redo

The Funnel Builder includes full history tracking, allowing you to reverse any accidental changes.

### How It Works

Every canvas mutation is captured in an in-memory undo stack (capped at 50 entries for performance). You can:
- **Undo** recent changes with `Cmd+Z` (Mac) or `Ctrl+Z` (Windows)
- **Redo** undone changes with `Cmd+Shift+Z` (Mac) or `Ctrl+Shift+Z` (Windows)
- Use the **toolbar undo/redo buttons** for visual feedback (disabled when stack is empty)

### What's Tracked

Undo/redo covers all canvas operations:
- Add, delete, move, or edit nodes
- Add, delete, or edit connections (edges)
- Add, delete, move, or edit text labels
- Multi-object operations (paste, duplicate, bulk delete)
- Import JSON / Clear canvas

### What's NOT Tracked

- **Viewport** (pan/zoom position) — not tracked to avoid cluttering history
- **Selection state** — resets to empty on undo/redo
- **CSV data uploads** — CSV data itself is not versioned, only node configurations

### Smart Debouncing

Property panel text edits (node labels, metric values, etc.) are **debounced** to avoid creating a new undo entry on every keystroke. After 500ms of inactivity, your edits are captured as a single undo entry. This keeps the history stack clean and makes undo/redo more predictable.

### Keyboard Shortcuts

- **Cmd+Z / Ctrl+Z**: Undo last action
- **Cmd+Shift+Z / Ctrl+Shift+Z**: Redo last undone action

These shortcuts work **everywhere**, even when focused on input fields in the properties panel.

### Toolbar Buttons

The toolbar includes visual undo/redo buttons (circular arrow icons) between the cursor mode toggle and "Add Node" button. These buttons:
- Show the current state (disabled/grayed when no history)
- Provide tooltips with keyboard shortcut hints
- Offer an alternative to keyboard shortcuts for discoverability

### History Stack Limit

The undo stack is limited to **50 entries** to prevent excessive memory usage. The oldest entries are automatically removed when the limit is reached. For most workflows, 50 steps is more than sufficient.

### Important Notes

- **Undo is permanent for the current session**: Once you close the browser tab, history is lost (only the final state is auto-saved to localStorage).
- **Redo stack clears on new action**: After undoing, if you make any new change, the redo stack is cleared (standard undo tree behavior).
- **Node dragging**: Dragging a node creates only **one** undo entry (captured at the start of the drag), not one per frame.
- **Multi-object drag**: Moving multiple selected objects together creates only **one** undo entry.

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

> [!TIP]
> You can combine multiple CSV metrics using **Derived Metrics** (see below) to create calculated fields like "Average Order Value" using the `self` keyword (e.g., `self.total_revenue / self.total_orders`).

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

## Global Filter Nodes

Global Filter Nodes allow you to apply scenario-wide filters that affect **all CSV data** across every node on the canvas. Unlike per-node filters (which only affect the specific node they're configured on), global filters are applied **before** any per-node filtering or aggregation happens.

### When to Use Global Filters

Use Global Filter Nodes when you want to:
- **Analyze specific scenarios**: Filter to a particular country, date range, or campaign across all nodes at once
- **Compare time periods**: Switch between different time ranges without reconfiguring every node
- **Focus on segments**: Filter to specific customer segments, product categories, or regions globally
- **Simplify complex funnels**: Instead of duplicating the same filter on 10 nodes, set it once globally

### Creating a Global Filter Node

1. **Upload CSV data first**: Global filters only appear when CSV data is loaded.
2. **Click "Add Global Filter"** in the toolbar (appears next to "Add Node" when CSV is loaded).
3. **Position the node**: The global filter node appears in the center of your canvas with an amber/orange theme to distinguish it from regular nodes.
4. **Configure filters**: Click the global filter node to open its properties panel and add filter conditions.

### Configuring Global Filters

The Global Filter properties panel has:

- **Label**: Name your global filter (e.g., "2024 Only" or "US Region Filter")
- **Global Filters section**:
  - **Column**: Select which CSV column to filter
  - **Operator**: Choose Equals, Not Equals, Contains, Greater Than, or Less Than
  - **Value**: Enter the filter value (autocomplete suggestions appear from your data)
  - **Row count indicator**: Shows how many rows match (e.g., "847 of 1000 rows match globally")
- **Add Filter button**: Add multiple filter conditions (they combine with AND logic)
- **Delete / Duplicate buttons**: Remove or copy the global filter node

### How Global Filters Work

**Processing Order:**
1. **Global filters** are applied to the raw CSV data first
2. **Per-node filters** are then applied to the globally-filtered dataset
3. **Aggregations** (SUM, AVG, etc.) are computed on the final filtered rows

**Example:**
```
Raw CSV Data: 1000 rows

↓ Global Filter: "Country = US"

Globally Filtered: 300 rows (this dataset is used by all nodes)

↓ Node A Filter: "Product = Shoes"     ↓ Node B Filter: "Product = Hats"

Node A: 150 rows                         Node B: 75 rows
SUM of Sales = $45,000                   SUM of Sales = $12,000
```

### Multiple Global Filter Nodes

You can create **multiple** global filter nodes, and their filters combine with **AND** logic (all conditions must match):

- **Global Filter A**: Country = "US"
- **Global Filter B**: Date > "2024-01-01"
- **Result**: Only rows where Country is US **AND** Date is after 2024-01-01

This is useful for building complex scenario filters step-by-step.

### Visual Design

Global Filter Nodes are visually distinct:
- **Amber/orange gradient background** (unlike the white background of regular nodes)
- **Filter icon** in the header
- **Filter summaries** displayed on the node body (e.g., "country = US", "date > 2024-01-01")
- **Row count badge** showing "X of Y rows match"
- **No connection ports** — global filters don't participate in the data flow graph

### Important Notes

- **No connections**: Global Filter Nodes cannot be connected to other nodes via edges. They affect all nodes globally by filtering the CSV dataset.
- **CSV required**: The "Add Global Filter" button only appears when CSV data is loaded.
- **Client-side processing**: All filtering happens in your browser — no data is sent externally.
- **Persistence**: Global filter nodes and their configurations are saved in localStorage and JSON exports.
- **Duplication**: You can duplicate global filter nodes using Ctrl+D / Cmd+D.
- **Deletion**: Deleting a global filter node immediately removes its filtering effect from all nodes.

### Use Cases

1. **Regional Analysis**: Create a global filter for "Region = North America" to analyze only that region's performance across your entire funnel.
2. **Date Range Comparison**: Set up global filters for different quarters and switch between them to compare performance.
3. **Campaign Isolation**: Filter to a specific marketing campaign ID to see how it performs across all conversion stages.
4. **Product Category Focus**: Filter to "Category = Electronics" to analyze just that product line across all metrics.

## Stream Filter Nodes

Stream Filter Nodes enable **flow-based filtering** where filters apply only to **downstream connected nodes** in the graph. Unlike Global Filter Nodes (which affect all nodes regardless of connections), Stream Filters create scoped data pipelines where different branches of your funnel can have different filter contexts.

### When to Use Stream Filters vs Global Filters

| Feature | Global Filter | Stream Filter |
|---------|---------------|---------------|
| **Scope** | Affects ALL nodes on canvas | Affects only downstream connected nodes |
| **Connections** | No ports, cannot be connected | Has input/output ports, participates in graph |
| **Use Case** | Scenario-wide filtering | Branch-specific filtering |
| **Example** | "Show only 2024 data across entire funnel" | "Show Facebook Ads performance in left branch, Google Ads in right branch" |

**Use Stream Filters when you want to:**
- Compare different segments side-by-side in the same funnel
- Create parallel processing pipelines with different filter contexts
- Apply progressive filtering where filters stack as data flows downstream
- Build complex multi-path funnels with branch-specific conditions

**Use Global Filters when you want to:**
- Apply a scenario-wide filter that affects every node
- Switch between different time periods or regions for the entire analysis
- Simplify your funnel by avoiding duplicate filters on every node

### Creating a Stream Filter Node

1. **Upload CSV data first**: Stream filters only appear when CSV data is loaded.
2. **Click "Add Stream Filter"** in the toolbar (appears next to "Add Global Filter" when CSV is loaded).
3. **Position and connect**: Stream filter nodes have **both input and output ports** — connect them between nodes to create filtered data flows.
4. **Configure filters**: Click the stream filter node to open its properties panel and add filter conditions.

### How Stream Filters Work

Stream filters create a **directional data flow pipeline**:

```
Global Filters (apply to all)
        ↓
Stream Filters (apply to downstream nodes only)
        ↓
Per-Node Filters (apply to individual node)
        ↓
Aggregation (SUM, AVG, etc.)
```

**Example 1: Simple Stream Filter**
```
Raw CSV: 1000 rows

    ↓

Stream Filter: "Country = US"

    ↓

Node A: Computes on US data only (300 rows)
```

**Example 2: Branching with Different Stream Filters**
```
                    Raw CSV: 1000 rows
                           ↓
                   ┌───────┴───────┐
                   ↓               ↓
    Stream Filter "Country = US"   Stream Filter "Country = UK"
                   ↓               ↓
        Node A (300 US rows)      Node B (200 UK rows)
```

Both Node A and Node B work on the same raw CSV, but see different filtered subsets.

**Example 3: Sequential Stream Filters (Transitive)**
```
Raw CSV: 1000 rows
        ↓
Stream Filter A: "Year = 2024"
        ↓ (500 rows)
Stream Filter B: "Country = US"
        ↓ (200 rows)
Node A: Receives data filtered by BOTH A and B
```

Stream filters are **transitive** — filters stack as you move downstream.

**Example 4: Multiple Paths Merging (AND Combination)**
```
                Raw CSV: 1000 rows
                       ↓
        ┌──────────────┴──────────────┐
        ↓                             ↓
Stream Filter A                 Stream Filter B
"Product = Shoes"              "Category = Electronics"
        ↓                             ↓
        └──────────────┬──────────────┘
                       ↓
                    Node C
        (Receives rows matching BOTH filters)
```

When a node is reachable through multiple paths with different stream filters, it receives the **intersection** (AND) of all filters.

### Configuring Stream Filters

The Stream Filter properties panel includes:

- **Label**: Name your stream filter (e.g., "US Region" or "2024 Data")
- **Stream Filters section**:
  - **Column**: Select which CSV column to filter
  - **Operator**: Choose Equals, Not Equals, Contains, Greater Than, or Less Than
  - **Value**: Enter the filter value (autocomplete suggestions from your data)
  - **Row count indicator**: Shows "X of Y rows match" (after global filters + this stream filter's own filters)
  - **Info text**: "These filters apply to all downstream connected nodes"
- **Add Filter button**: Add multiple filter conditions (combine with AND logic)
- **Delete / Duplicate buttons**: Remove or copy the stream filter node

### Visual Design

Stream Filter Nodes are visually distinct from both regular nodes and global filter nodes:
- **Cyan/teal gradient background** (unlike white for regular nodes, amber for global filters)
- **Flow filter icon** in the header (funnel with arrow)
- **Input AND output ports** (unlike global filter nodes which have no ports)
- **Filter summaries** displayed on node body (e.g., "country = US")
- **Row count badge** showing "X of Y rows match"

### Filter Application Order

The complete filter pipeline is:

1. **Global Filters** → Applied to raw CSV data (affects all nodes)
2. **Stream Filters** → Applied to globally-filtered data (affects downstream nodes only)
3. **Per-Node Filters** → Applied to stream-filtered data (affects individual node only)
4. **Aggregation** → SUM, AVG, COUNT, etc. computed on final filtered rows

### Use Cases

1. **Multi-Channel Comparison**:
   ```
   Raw Data → Stream Filter "Source = Facebook" → Facebook Funnel
           ↘ Stream Filter "Source = Google" → Google Funnel
   ```
   Analyze different marketing channels side-by-side in the same canvas.

2. **Regional Analysis with Sub-Segments**:
   ```
   Global Filter: "Year = 2024"
           ↓
   Stream Filter: "Region = North America"
           ↓
   ┌───────┴───────┐
   ↓               ↓
   "Product = A"   "Product = B"
   ```
   Filter to a year globally, then split by region and product using stream filters.

3. **Progressive Filtering Pipeline**:
   ```
   Raw → Filter "Active = Yes" → Filter "Premium = Yes" → Filter "Churned = No" → Final Segment
   ```
   Stack multiple stream filters to progressively narrow down to a specific segment.

4. **A/B Test Comparison**:
   ```
   Raw → Stream Filter "Variant = A" → Variant A Metrics
      ↘ Stream Filter "Variant = B" → Variant B Metrics
   ```
   Compare test variants in parallel pipelines.

### Important Notes

- **Connections required**: Unlike global filters, stream filters must be connected to nodes via edges to have any effect.
- **Directional flow**: Stream filters only affect nodes that are **downstream** (reachable by following outgoing edges).
- **Upstream unaffected**: Nodes upstream of a stream filter are not affected by it.
- **Stacking behavior**: Multiple stream filters in sequence combine with AND logic (all conditions must match).
- **Client-side processing**: All filtering happens in your browser — no data is sent externally.
- **Persistence**: Stream filter nodes and their configurations are saved in localStorage and JSON exports.
- **Duplication**: Use Ctrl+D / Cmd+D to duplicate stream filter nodes.
- **No metrics**: Stream Filter nodes don't compute CSV metrics or derived metrics — they only filter data for downstream nodes.

### Stream Filter vs Global Filter: Quick Reference

Choose **Global Filter** when:
- ✓ You want to filter all nodes on the canvas
- ✓ You're analyzing a single scenario (e.g., "2024 only")
- ✓ You want to switch filter contexts for the entire funnel at once

Choose **Stream Filter** when:
- ✓ You want to compare different segments side-by-side
- ✓ You need different filter contexts for different branches
- ✓ You're building a multi-path funnel with branch-specific conditions
- ✓ You want to progressively filter data as it flows through the pipeline

## Derived Metrics (Multi-Source Data Input)

Derived Metrics allow you to create true data pipelines where metric values flow from upstream (source) nodes to downstream (target) nodes. Unlike edge calculations (which only display results on the connection), derived metrics become **first-class metrics on the target node** — available for display, downstream calculations, and further derivation.

Additionally, nodes with CSV data can use derived metrics to create **internal calculated fields** by referencing their own metrics, even without any incoming connections.

### How It Works

When a node has incoming connections (edges) **or** has a CSV file loaded with configured metrics, you can define **Derived Metrics** that compute values based on:
1. **Source nodes**: Metrics from any or all connected upstream nodes.
2. **Current node (self)**: The node's own CSV-aggregated or manual metrics.

The computation follows topological order, so derived metrics can reference other derived metrics in a chain.

### Creating a Derived Metric

1. **Connect nodes** (optional): Create edges from one or more source nodes to your target node.
2. **Upload CSV** (optional): Configure CSV metrics on the node.
3. **Select the target node**: Click the node to open the properties panel.
4. **Add a Derived Metric**: In the "Derived Metrics" section, click **"+ Add Derived Metric"**.
   - *Note: This section appears if the node has incoming edges OR if it has CSV metrics defined.*
5. **Configure the metric**:
   - **Key**: A unique identifier for the metric (e.g., `total_leads`).
   - **Label**: Display name (e.g., "Total Leads from All Sources").
   - **Expression**: Formula referencing metrics using `<node_label>.<metric_key>` or `self.<metric_key>`.
   - **Computed Value**: Shows the live result (read-only).

### Expression Syntax

Derived metric expressions reference source nodes using **sanitized versions of their labels**:

| Syntax | Meaning |
|--------|---------|
| `facebook_ads.lead_count` | Metric `lead_count` from the source node labeled "Facebook Ads" |
| `google_ads.revenue` | Metric `revenue` from the source node labeled "Google Ads" |
| `self.lead_count` | Metric `lead_count` from the **current node** (internal calculation) |
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

### Example 4: Internal Calculated Fields (Self-Referencing)

```
Node: Sales Data (CSV Metrics)
├─ sum_revenue = 50000
├─ count_orders = 1000

    [Internal Derived Metric]
    ├─ aov = self.sum_revenue / self.count_orders → 50
```

In this example:
- The node uses the `self` keyword to combine its own CSV-aggregated metrics.
- No incoming connections are required to use `self` references.
- These internal calculated fields can then flow downstream to other nodes.

### Advanced Features

- **Self-Referencing**: Use `self.<key>` to reference the node's own CSV and manual metrics.
- **Multi-Source Expressions**: A single derived metric expression can reference metrics from **any or all** connected upstream nodes, as well as `self`.
- **Mixed Metric Types**: Derived metrics, CSV metrics, and manual metrics all coexist on the same node.
- **Cycle Detection**: If a circular dependency is detected (A → B → A), the tool prevents infinite loops and shows a warning.
- **Disconnection Warnings**: If an edge is deleted and a derived metric still references the disconnected node, a warning badge appears showing which references are broken. Note that `self` references are always considered valid if the node has the corresponding metric.
- **Topological Computation**: Metrics are computed in dependency order automatically, so complex chains always resolve correctly.
- **Backward Compatibility**: Old saved funnels using the previous single-source format are automatically migrated to the new multi-source syntax on load. Existing expressions are unaffected.

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
      "id": "global-filter-1",
      "type": "global-filter",
      "label": "2024 US Region",
      "position": { "x": 96, "y": 200 },
      "color": 3,
      "globalFilters": [
        { "column": "country", "operator": "equals", "value": "US" },
        { "column": "year", "operator": "equals", "value": "2024" }
      ]
    },
    {
      "id": "stream-filter-1",
      "type": "stream-filter",
      "label": "Facebook Ads Only",
      "position": { "x": 96, "y": 350 },
      "color": 5,
      "streamFilters": [
        { "column": "lead_source", "operator": "equals", "value": "Facebook Ads" }
      ]
    },
    {
      "id": "node-1",
      "type": "node",
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
      "type": "node",
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
| Shift+Click | Toggle object in/out of multi-selection |
| Click + drag (canvas) | Draw selection box to select multiple objects |
| Shift + drag selection | Add to existing selection with box select |
| Ctrl+Z / Cmd+Z | Undo last action |
| Ctrl+Shift+Z / Cmd+Shift+Z | Redo last undone action |
| Ctrl+C / Cmd+C | Copy selected objects to clipboard |
| Ctrl+V / Cmd+V | Paste copied objects (with new IDs, offset +40px) |
| Ctrl+D / Cmd+D | Duplicate selected objects |
| Delete / Backspace | Delete selected objects |
| Escape | Cancel connection / clear selection |
| H | Switch to Hand tool (pan mode) |
| V | Switch to Select tool (box selection mode) |
| Spacebar + drag | Pan canvas |
| Middle-click + drag | Pan canvas |
| Scroll wheel / 2-finger swipe | Pan canvas |
| Ctrl+Scroll / Pinch | Zoom in/out |
| Click + drag (node) | Move node (or all selected nodes together) |

## Privacy

All data processing happens entirely in the browser. No data is sent to any server.
