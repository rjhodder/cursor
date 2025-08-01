# 2x2 Magnet Layout App - Quick Reference

## Public Functions

### Core Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `drawTemplate()` | None | `void` | Renders complete magnet template on canvas |
| `toggleDarkMode()` | None | `void` | Switches between light/dark themes |
| `toggleCutMarks()` | None | `void` | Shows/hides cutting guides |
| `generateSheet()` | None | `Promise<void>` | Creates PDF with 12 magnets (3×4 grid) |

## Global Variables

| Variable | Type | Initial Value | Description |
|----------|------|---------------|-------------|
| `uploadedImage` | `Image \| undefined` | `undefined` | Currently loaded image object |
| `brandingText` | `string` | `""` | User-entered branding text |
| `showCutMarks` | `boolean` | `true` | Cut marks visibility state |
| `imageX` | `number` | `0` | Image X position on canvas |
| `imageY` | `number` | `0` | Image Y position on canvas |
| `isDragging` | `boolean` | `false` | Mouse drag state |

## Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `bleedSize` | `675` | Canvas size including bleed area |
| `trimSize` | `600` | Final magnet size (2" at 300 DPI) |
| `safeSize` | `525` | Safe area for important content |
| `margin` | `37.5` | Bleed margin in pixels |
| `safeMargin` | `75` | Safe area margin in pixels |

## Event Listeners

| Element | Event | Description |
|---------|-------|-------------|
| `#imageLoader` | `change` | Handles image file upload |
| `#brandingText` | `input` | Updates branding text in real-time |
| `canvas` | `mousedown` | Starts image dragging |
| `canvas` | `mouseup` | Ends image dragging |
| `canvas` | `mousemove` | Handles image repositioning during drag |

## HTML Elements

| ID | Type | Purpose |
|----|------|---------|
| `templateCanvas` | `<canvas>` | Main design surface (675×675px) |
| `imageLoader` | `<input type="file">` | Image upload control |
| `brandingText` | `<input type="text">` | Branding text input |

## CSS Classes

| Class | Description |
|-------|-------------|
| `.dark-mode` | Applied to body for dark theme |
| `.container` | Main content wrapper (max-width: 800px) |

## Quick Usage Examples

```javascript
// Basic operations
drawTemplate();           // Redraw canvas
toggleDarkMode();         // Switch theme
toggleCutMarks();         // Toggle guides
await generateSheet();    // Create PDF

// Programmatic text setting
document.getElementById("brandingText").value = "My Brand";
document.getElementById("brandingText").dispatchEvent(new Event('input'));

// Check current state
console.log(showCutMarks);    // Cut marks visibility
console.log(brandingText);    // Current branding text
console.log(uploadedImage);   // Current image (if any)
```

## PDF Generation Details

- **Output:** `magnet_sheet.pdf`
- **Layout:** 3 columns × 4 rows (12 magnets)
- **Size:** Letter (8.5" × 11")
- **Magnet size:** 2.25" × 2.25"
- **Gap:** 0.2" between magnets

## Dependencies

- **jsPDF 2.5.1+** - Required for PDF generation
- **Modern browser** - Canvas, FileReader, CSS Custom Properties support