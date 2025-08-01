# 2x2 Magnet Layout App - API Documentation

## Overview

The 2x2 Magnet Layout App is a web-based application for designing custom 2" x 2" magnets. It provides an interactive canvas for image placement, text branding, visual guides, and PDF generation for professional printing.

## Table of Contents

1. [Application Setup](#application-setup)
2. [Core Functions](#core-functions)
3. [Event Handlers](#event-handlers)
4. [Configuration](#configuration)
5. [UI Components](#ui-components)
6. [Usage Examples](#usage-examples)
7. [PDF Generation](#pdf-generation)

---

## Application Setup

### Canvas Initialization

The application uses HTML5 Canvas for rendering the magnet design.

```javascript
const canvas = document.getElementById("templateCanvas");
const ctx = canvas.getContext("2d");
```

**Canvas Specifications:**
- Width: 675px
- Height: 675px
- Resolution: 300 DPI equivalent for 2.25" x 2.25" (including bleed)

---

## Core Functions

### `drawTemplate()`

**Purpose:** Renders the complete magnet template including background, uploaded image, cut marks, and branding text.

**Parameters:** None

**Returns:** `void`

**Usage:**
```javascript
drawTemplate(); // Redraws the entire canvas
```

**Features:**
- Clears previous canvas content
- Draws background (#f0f0f0)
- Renders uploaded image at current position
- Displays cut marks (red trim line, green dashed safe area)
- Renders branding text

---

### `toggleDarkMode()`

**Purpose:** Switches between light and dark theme modes.

**Parameters:** None

**Returns:** `void`

**Usage:**
```javascript
toggleDarkMode(); // Toggles between light/dark themes
```

**Implementation:**
- Toggles `.dark-mode` CSS class on document body
- Uses CSS custom properties for theme colors:
  - Light mode: `--bg: #f9f9f9`, `--fg: #000`
  - Dark mode: `--bg: #1e1e1e`, `--fg: #fff`

---

### `toggleCutMarks()`

**Purpose:** Shows or hides the cutting guides and safe area markers.

**Parameters:** None

**Returns:** `void`

**Usage:**
```javascript
toggleCutMarks(); // Toggles visibility of cut marks
```

**Cut Mark Types:**
- **Red solid line:** Trim area (600x600px)
- **Green dashed line:** Safe area (525x525px)

---

### `generateSheet()`

**Purpose:** Generates a printable PDF sheet with multiple magnet copies arranged for professional printing.

**Parameters:** None

**Returns:** `Promise<void>`

**Usage:**
```javascript
await generateSheet(); // Creates and downloads PDF
```

**PDF Specifications:**
- Format: Letter size (8.5" x 11")
- Layout: 3 columns × 4 rows (12 magnets total)
- Magnet size: 2.25" x 2.25"
- Gap between magnets: 0.2"
- Auto-centered on page

---

## Event Handlers

### Image Upload Handler

**Trigger:** File input change event

**Supported formats:** All image types (`image/*`)

```javascript
document.getElementById("imageLoader").addEventListener("change", function (e) {
    // Handles image upload and processing
});
```

**Process:**
1. Uses FileReader to read selected file
2. Creates new Image object
3. Automatically redraws template when loaded

---

### Branding Text Handler

**Trigger:** Text input change event

```javascript
document.getElementById("brandingText").addEventListener("input", function (e) {
    brandingText = e.target.value;
    drawTemplate();
});
```

**Text Specifications:**
- Font: 30px sans-serif
- Color: Black
- Position: Centered, 20px from bottom
- Updates in real-time

---

### Image Dragging Handlers

**Mouse Events:** `mousedown`, `mouseup`, `mousemove`

```javascript
// Enable image repositioning
canvas.addEventListener("mousedown", (e) => {
    isDragging = true;
});

canvas.addEventListener("mousemove", (e) => {
    if (isDragging && uploadedImage) {
        // Calculate new position and redraw
    }
});
```

**Features:**
- Click and drag to reposition uploaded image
- Real-time preview during drag
- Coordinates calculated relative to canvas bounds

---

## Configuration

### Design Specifications

```javascript
const bleedSize = 675;    // Full canvas size including bleed
const trimSize = 600;     // Final magnet size (2" at 300 DPI)
const safeSize = 525;     // Safe area for text/important elements
const margin = 37.5;      // Bleed margin ((675-600)/2)
const safeMargin = 75;    // Safe area margin ((675-525)/2)
```

### Global Variables

```javascript
let uploadedImage;        // Image object for uploaded file
let brandingText = "";    // User-entered branding text
let showCutMarks = true;  // Cut marks visibility toggle
let imageX = 0;          // Image X position
let imageY = 0;          // Image Y position
let isDragging = false;   // Mouse drag state
```

---

## UI Components

### File Input

```html
<input type="file" id="imageLoader" accept="image/*" />
```

**Purpose:** Upload images for magnet design
**Accepts:** All image formats

### Text Input

```html
<input type="text" id="brandingText" placeholder="Enter branding text" />
```

**Purpose:** Add custom branding text to design
**Features:** Real-time preview

### Control Buttons

```html
<button onclick="toggleDarkMode()">Toggle Dark Mode</button>
<button onclick="toggleCutMarks()">Toggle Cut Marks</button>
<button onclick="generateSheet()">Generate Full Sheet PDF</button>
```

### Canvas Element

```html
<canvas id="templateCanvas" width="675" height="675"></canvas>
```

**Purpose:** Interactive design surface
**Features:** Image dragging, real-time preview

---

## Usage Examples

### Basic Setup

```html
<!DOCTYPE html>
<html>
<head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
</head>
<body>
    <!-- Include the magnet layout HTML structure -->
</body>
</html>
```

### Creating a Custom Magnet

1. **Upload an Image:**
   ```javascript
   // User selects file through file input
   // Image automatically loads and displays
   ```

2. **Add Branding Text:**
   ```javascript
   document.getElementById("brandingText").value = "My Company";
   // Text appears at bottom of magnet
   ```

3. **Position Image:**
   ```javascript
   // Click and drag image on canvas to desired position
   ```

4. **Generate PDF:**
   ```javascript
   await generateSheet(); // Downloads "magnet_sheet.pdf"
   ```

### Programmatic Control

```javascript
// Set branding text programmatically
document.getElementById("brandingText").value = "Custom Text";
document.getElementById("brandingText").dispatchEvent(new Event('input'));

// Toggle features
toggleDarkMode();    // Switch theme
toggleCutMarks();    // Hide/show guides

// Generate PDF programmatically
generateSheet().then(() => {
    console.log("PDF generation complete");
});
```

---

## PDF Generation

### Sheet Layout

- **Page Size:** 8.5" × 11" (US Letter)
- **Magnets per sheet:** 12 (3 columns × 4 rows)
- **Individual magnet size:** 2.25" × 2.25"
- **Spacing:** 0.2" gap between magnets
- **Margins:** Auto-calculated to center the layout

### Print Specifications

```javascript
const magnetsPerRow = 3;      // Horizontal count
const magnetsPerCol = 4;      // Vertical count
const gap = 0.2;              // Gap between magnets (inches)
const magnetInches = 2.25;    // Final magnet size (inches)
```

### Output File

- **Filename:** `magnet_sheet.pdf`
- **Format:** PDF/A compatible
- **Resolution:** 300 DPI equivalent
- **Color space:** RGB

---

## CSS Custom Properties

### Theme Variables

```css
:root {
    --bg: #f9f9f9;    /* Light background */
    --fg: #000;       /* Light text */
}

.dark-mode {
    --bg: #1e1e1e;    /* Dark background */
    --fg: #fff;       /* Dark text */
}
```

### Responsive Design

```css
.container {
    max-width: 800px;
    margin: auto;
    text-align: center;
}

canvas {
    border: 1px solid #ccc;
    margin: 20px 0;
    cursor: move;
}
```

---

## Browser Compatibility

### Required Features
- HTML5 Canvas support
- FileReader API
- ES6+ JavaScript features
- CSS Custom Properties

### Supported Browsers
- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

### Dependencies
- **jsPDF 2.5.1+:** PDF generation library
- **Modern browser:** Canvas and FileReader support

---

## Error Handling

### Common Issues

1. **Image Upload Failures:**
   - Unsupported file formats
   - File size limitations
   - Network connectivity issues

2. **PDF Generation Errors:**
   - Missing jsPDF library
   - Canvas rendering issues
   - Browser popup blockers

3. **Performance Considerations:**
   - Large image files may cause slow rendering
   - Multiple rapid redraws can impact performance
   - PDF generation with many elements may be slow

### Best Practices

- Recommend image sizes under 5MB
- Use common formats (JPG, PNG, GIF)
- Test PDF generation before production use
- Provide user feedback during long operations

---

## Advanced Customization

### Modifying Canvas Size

```javascript
// Update canvas dimensions
const customSize = 800;
canvas.width = customSize;
canvas.height = customSize;

// Update drawing calculations accordingly
const bleedSize = customSize;
// Recalculate other dimensions...
```

### Custom PDF Layout

```javascript
// Modify generateSheet() for different layouts
const magnetsPerRow = 4;  // Change grid size
const magnetsPerCol = 3;
const magnetInches = 2.0; // Change magnet size
```

### Adding New Features

```javascript
// Example: Add rotation capability
let imageRotation = 0;

function rotateImage() {
    imageRotation += 90;
    drawTemplate();
}

// Modify drawTemplate() to apply rotation
ctx.save();
ctx.translate(imageX + bleedSize/2, imageY + bleedSize/2);
ctx.rotate(imageRotation * Math.PI / 180);
ctx.drawImage(uploadedImage, -bleedSize/2, -bleedSize/2, bleedSize, bleedSize);
ctx.restore();
```

---

This documentation provides comprehensive coverage of all public APIs, functions, and components in the 2x2 Magnet Layout App. Each section includes practical examples and implementation details for effective usage and customization.