# Confluence HTML Format Reference

Reliable round-trip conversion between Confluence ADF and human-readable HTML. Prefer these documented patterns rather than inventing custom markup.

## Block elements

| Element | HTML Pattern |
|---------|-------------|
| Heading | `<h1>` through `<h6>` |
| Paragraph | `<p>text</p>` |
| Bullet list | `<ul><li>item</li></ul>` |
| Ordered list | `<ol start="1"><li>item</li></ol>` |
| Task list | `<ul data-type="task-list"><li data-type="task-item"><input type="checkbox"> Todo</li></ul>` |
| Checked task | `<li data-type="task-item"><input type="checkbox" checked> Done</li>` |
| Decision list | `<ul data-type="decision-list"><li data-type="decision-item" data-state="DECIDED">Approved</li></ul>` |
| Table | `<table><thead><tr><th>Header</th></tr></thead><tbody><tr><td>Cell</td></tr></tbody></table>` |
| Blockquote | `<blockquote><p>quoted text</p></blockquote>` |
| Code block | `<pre><code class="language-python">code here</code></pre>` |
| Horizontal rule | `<hr>` |
| Info panel | `<div data-type="panel-info"><p>Info text</p></div>` |
| Warning panel | `<div data-type="panel-warning"><p>Warning text</p></div>` |
| Note panel | `<div data-type="panel-note"><p>Note text</p></div>` |
| Success panel | `<div data-type="panel-success"><p>Success text</p></div>` |
| Error panel | `<div data-type="panel-error"><p>Error text</p></div>` |
| Custom panel | `<div data-type="panel-custom" data-icon=":emoji:" data-color="#hex"><p>text</p></div>` |
| Expand | `<details><summary>Title</summary><p>Hidden content</p></details>` |
| Layout (2 col) | `<section data-type="layout-two-equal"><div data-type="column"><p>Left</p></div><div data-type="column"><p>Right</p></div></section>` |
| Block card | `<div data-type="block-card" data-url="URL"><a href="URL">URL</a></div>` |
| Embed card | `<div data-type="embed-card" data-layout="center"><iframe src="URL"></iframe></div>` |
| Block extension | `<div data-type="extension" data-extension-key="KEY" data-extension-type="TYPE">content</div>` |
| Bodied extension | `<div data-type="bodied-extension" data-extension-key="KEY" data-extension-type="TYPE"><p>body</p></div>` |

**Decision states:** `DECIDED`, `UNDECIDED`

**Layout types** (on `<section data-type="...">`)`:` `layout-two-equal`, `layout-two-left-sidebar`, `layout-two-right-sidebar`, `layout-three-equal`, `layout-three-with-sidebars`

### Table options

| Option | HTML Attribute | Description |
|--------|---------------|-------------|
| Header row | `<thead>` with `<th>` cells | First row as header |
| Header column | `<th>` for first cell in `<tbody>` rows | First column as header |
| Numbered rows | `data-number-column="true"` on `<table>` | Auto row numbering |
| Fixed widths | `data-display-mode="fixed"` on `<table>` | Use with `data-colwidth` on cells |
| Table sizing | `data-layout` on `<table>` | `default`, `center`, `wide`, `full-width` |

Cell merge: `colspan` and `rowspan` attributes on `<th>`/`<td>`.
Cell background: `style="background-color: #HEX"` on `<th>`/`<td>`.

## Inline elements

| Element | HTML Pattern |
|---------|-------------|
| Bold | `<strong>text</strong>` |
| Italic | `<em>text</em>` |
| Underline | `<u>text</u>` |
| Strikethrough | `<s>text</s>` |
| Inline code | `<code>text</code>` |
| Subscript | `<sub>text</sub>` |
| Superscript | `<sup>text</sup>` |
| Link | `<a href="URL">text</a>` |
| Smart link | `<a href="URL" data-card-appearance="inline">text</a>` |
| Text color | `<span style="color: #HEX">text</span>` |
| Background highlight | `<span style="background-color: #HEX">text</span>` |
| Line break | `<br>` |
| Status lozenge | `<span data-type="status" data-color="green">Done</span>` |
| Emoji | `<span data-type="emoji" data-shortname=":rocket:">🚀</span>` |
| Date | `<time datetime="2025-03-15">March 15, 2025</time>` |
| Mention | `<span data-type="mention" data-user-id="ACCOUNT_ID">@Name</span>` |
| Placeholder | `<span data-type="placeholder">Type something…</span>` |

**Status colors:** `neutral`, `purple`, `blue`, `red`, `yellow`, `green`

## Text alignment and indentation

```html
<p style="text-align: center">Centered</p>
<p style="margin-left: 30px">Indented 1 level (30px per level)</p>
```

## Media / Attachment elements

After uploading an attachment, use the returned `MEDIA_ID` and `COLLECTION` values:

```html
<!-- Image with optional caption -->
<figure data-type="media-single" data-layout="center" data-width="80">
  <div data-type="media" data-media-type="file" data-id="MEDIA_ID" data-collection="COLLECTION">filename.png</div>
  <figcaption>Optional caption</figcaption>
</figure>

<!-- Non-image attachment (file link) -->
<div data-type="media-group">
  <div data-type="media" data-media-type="file" data-id="MEDIA_ID" data-collection="COLLECTION">document.pdf</div>
</div>

<!-- Inline file reference -->
<p>See <span data-type="media-inline" data-id="MEDIA_ID" data-collection="COLLECTION">report.pdf</span></p>

<!-- External image (no attachment needed) -->
<img src="https://example.com/image.png" alt="description">
```

**Figure `data-layout`:** `center`, `wrap-left`, `wrap-right`, `wide`, `full-width`, `align-start`, `align-end`
**Figure `data-width`:** percentage (e.g. `80`). Set on `<figure>`, not inner `<div>`.

## Table charts

Wrap a table in `<div data-type="chart">` to create a Confluence chart. First column = x-axis; remaining columns = data series.

```html
<div data-type="chart" data-chart-type="BAR" data-chart-title="Revenue">
  <table>
    <thead><tr><th>Quarter</th><th>Product A</th></tr></thead>
    <tbody><tr><td>Q1</td><td>12</td></tr></tbody>
  </table>
</div>
```

| Attribute | Default | Values |
|-----------|---------|--------|
| `data-chart-type` | `BAR` | `BAR`, `LINE`, `PIE`, `DONUT`, `AREA` |
| `data-chart-title` | — | Title text |
| `data-chart-y-label` | — | Y-axis label |
| `data-chart-x-label` | — | X-axis label |
| `data-chart-colors` | `#4688EC` | Comma-separated hex, one per series |
| `data-chart-height` | `350` | Pixels |
| `data-chart-orientation` | `vertical` | `vertical`, `horizontal` |

## Important rules

- **No wrapper tags** — never use `<html>`, `<head>`, or `<body>`. Provide content elements only.
- **Well-formed HTML** — all tags must be properly opened and closed.
- **Semantic tags** — prefer `<strong>` over `<b>`, `<em>` over `<i>`.
- **Escape special characters** — use `&lt;`, `&gt;`, `&amp;`, `&quot;`.
- **Don't set `id` attributes** — Confluence manages these.
- All patterns in this reference round-trip losslessly between HTML and ADF.
