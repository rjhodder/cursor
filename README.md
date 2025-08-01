# 2x2 Magnet Layout App Documentation

## Overview

The **2x2 Magnet Layout App** is a web-based application for designing custom 2" x 2" magnets with professional printing capabilities. It provides an intuitive interface for image upload, positioning, text branding, and PDF generation for commercial printing.

## 🚀 Features

- **Interactive Canvas Design** - Drag and drop image positioning with real-time preview
- **Professional Print Output** - Generate print-ready PDFs with proper bleed and cut marks  
- **Batch Processing** - Create multiple magnet designs efficiently
- **Theme Support** - Light and dark mode interface
- **High Resolution** - 300 DPI equivalent output for professional printing
- **Multiple Layouts** - Flexible PDF layouts (3×4 standard, custom arrangements)

## 📁 Documentation Structure

This documentation suite includes comprehensive guides for developers and users:

### 📖 Main Documentation

| File | Description | Best For |
|------|-------------|----------|
| **[API_DOCUMENTATION.md](./API_DOCUMENTATION.md)** | Complete API reference with detailed function descriptions | Understanding all available functions and their usage |
| **[QUICK_REFERENCE.md](./QUICK_REFERENCE.md)** | Concise function reference and lookup tables | Quick development reference |
| **[EXAMPLES.md](./EXAMPLES.md)** | Practical usage examples and implementation patterns | Learning through real-world code examples |

### 🎯 Quick Start

1. **View the App**: Open `magnetlayout.html` in a modern browser
2. **Explore the API**: Start with [QUICK_REFERENCE.md](./QUICK_REFERENCE.md) for function overview
3. **Learn Integration**: Check [EXAMPLES.md](./EXAMPLES.md) for implementation patterns  
4. **Deep Dive**: Use [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) for complete details

## 🔧 Core Functions

| Function | Purpose | Returns |
|----------|---------|---------|
| `drawTemplate()` | Renders the magnet design on canvas | `void` |
| `toggleDarkMode()` | Switches between light/dark themes | `void` |
| `toggleCutMarks()` | Shows/hides cutting guides | `void` |
| `generateSheet()` | Creates printable PDF with 12 magnets | `Promise<void>` |

## 📐 Technical Specifications

### Canvas & Design
- **Canvas Size**: 675×675 pixels
- **Print Resolution**: 300 DPI equivalent
- **Final Magnet Size**: 2" × 2" (600×600 px)
- **Bleed Area**: 0.125" (37.5 px) on all sides
- **Safe Area**: 1.75" × 1.75" (525×525 px)

### PDF Output
- **Sheet Size**: 8.5" × 11" (US Letter)
- **Layout**: 3 columns × 4 rows (12 magnets)
- **Magnet Size**: 2.25" × 2.25" (including bleed)
- **Gap**: 0.2" between magnets
- **Format**: PDF with embedded images

### Browser Support
- Chrome 60+, Firefox 55+, Safari 12+, Edge 79+
- Requires: HTML5 Canvas, FileReader API, CSS Custom Properties

## 🛠️ Dependencies

- **[jsPDF 2.5.1+](https://github.com/parallax/jsPDF)** - PDF generation library
- **Modern Browser** - Canvas and FileReader support required

## 📋 Common Use Cases

### Basic Usage
```javascript
// Upload and position image
// Add branding text  
// Generate PDF for printing
await generateSheet();
```

### Programmatic Control
```javascript
// Load image from URL
await loadImageFromURL('logo.png');

// Set branding text
setBrandingText('My Company');

// Position image
imageX = 50; imageY = -20;
drawTemplate();

// Generate PDF
await generateSheet();
```

### Batch Processing
```javascript
// Process multiple designs
const designs = [
    { imageUrl: 'logo1.png', text: 'Company A' },
    { imageUrl: 'logo2.png', text: 'Company B' }
];

for (const design of designs) {
    await loadImageFromURL(design.imageUrl);
    setBrandingText(design.text);
    await generateSheet();
}
```

## 🎨 Customization Examples

### Custom PDF Layouts
```javascript
// Create 4×5 dense layout
generateCustomSheet({
    rows: 5, cols: 4,
    magnetSize: 1.8,
    gapX: 0.1, gapY: 0.1,
    filename: "dense_sheet.pdf"
});
```

### Image Effects
```javascript
// Apply filters
applyImageEffect('grayscale');
applyImageEffect('sepia');
rotateImage(90);
```

### Event Handling
```javascript
// Listen for design changes
designerEvents.addEventListener('templateDrawn', (e) => {
    console.log('Design updated:', e.detail);
});
```

## 🔍 Documentation Navigation

### For Developers

1. **Getting Started** → [QUICK_REFERENCE.md](./QUICK_REFERENCE.md)
   - Function signatures and basic usage
   - Global variables and constants
   - HTML elements and CSS classes

2. **Implementation Guide** → [EXAMPLES.md](./EXAMPLES.md) 
   - Integration patterns
   - Workflow automation
   - Error handling
   - Performance optimization

3. **Complete Reference** → [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)
   - Detailed function documentation
   - Configuration options
   - Advanced customization
   - Browser compatibility

### For Integrators

- **Basic Integration**: See [EXAMPLES.md - Basic Integration](./EXAMPLES.md#basic-integration)
- **Custom Workflows**: See [EXAMPLES.md - Custom Workflows](./EXAMPLES.md#custom-workflows)  
- **Error Handling**: See [EXAMPLES.md - Error Handling](./EXAMPLES.md#error-handling)

### For API Users

- **Function Reference**: See [QUICK_REFERENCE.md - Public Functions](./QUICK_REFERENCE.md#public-functions)
- **Event System**: See [API_DOCUMENTATION.md - Event Handlers](./API_DOCUMENTATION.md#event-handlers)
- **Configuration**: See [API_DOCUMENTATION.md - Configuration](./API_DOCUMENTATION.md#configuration)

## 🚨 Important Notes

### File Size Recommendations
- **Images**: Keep under 5MB for optimal performance
- **Formats**: JPG, PNG, GIF recommended
- **Resolution**: 300 DPI for best print quality

### Performance Considerations
- Large images may cause slow rendering
- Use debounced drawing for smooth interactions
- Clear image cache periodically to prevent memory leaks

### Print Specifications
- Designs include 0.125" bleed on all sides
- Cut marks indicate trim and safe areas
- PDF output is optimized for commercial printing

## 🤝 Contributing

When extending the application:

1. Follow the existing API patterns
2. Include comprehensive error handling
3. Update documentation for new features
4. Test across supported browsers
5. Maintain performance standards

## 📞 Support

For technical questions or implementation help:

- Review the [API Documentation](./API_DOCUMENTATION.md) for complete details
- Check [Examples](./EXAMPLES.md) for implementation patterns
- Use [Quick Reference](./QUICK_REFERENCE.md) for function lookups

---

**Version**: 1.0.0  
**Last Updated**: 2024  
**License**: MIT