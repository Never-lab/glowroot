# Trace point markers — classic parity (React `/modern`)

Date: 2026-11-16

## Problem

`TracePointChart` uses circle / triangle / diamond for normal / error / partial.
Classic Flot UI uses **circles only**, differentiated by color. Triangles/diamonds read as noisy and non-parity.

## Decision

PR1 (this): all series as circles; keep classic colors and z-order.

## Spec

| Series | Shape | Color |
|--------|-------|-------|
| Normal | circle | `#edc240` |
| Error | circle | `#bf380b` |
| Active/Partial | circle | `#417998` |

- Highlight: larger radius + stronger stroke (unchanged behavior)
- Legend: three colored circles + same labels
- Draw order: normal → error → partial (partial on top)
- Out of scope: Trace modal layout, filters, zoom, global palette

## Success

- Scatter on Transaction → Traces shows only circles
- e2e selectors still find `.trace-point-circle` (no dependency on triangle/diamond)
