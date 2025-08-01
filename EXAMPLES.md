# 2x2 Magnet Layout App - Usage Examples

## Table of Contents

1. [Basic Integration](#basic-integration)
2. [Programmatic Control](#programmatic-control)
3. [Custom Workflows](#custom-workflows)
4. [Advanced Customizations](#advanced-customizations)
5. [Event Handling](#event-handling)
6. [Error Handling](#error-handling)
7. [Performance Optimization](#performance-optimization)

---

## Basic Integration

### Minimal HTML Setup

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Custom Magnet Designer</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
</head>
<body>
    <div class="container">
        <h1>Magnet Designer</h1>
        <input type="file" id="imageLoader" accept="image/*" />
        <input type="text" id="brandingText" placeholder="Enter text" />
        <canvas id="templateCanvas" width="675" height="675"></canvas>
        <button onclick="generateSheet()">Download PDF</button>
    </div>
    
    <!-- Include the magnet layout script here -->
</body>
</html>
```

### Simple Workflow

```javascript
// 1. Wait for DOM to load
document.addEventListener('DOMContentLoaded', function() {
    // Initialize the canvas
    drawTemplate();
});

// 2. Handle image upload
document.getElementById('imageLoader').addEventListener('change', function(e) {
    // Image processing is handled automatically
    console.log('Image uploaded successfully');
});

// 3. Set branding text
function setBrandingText(text) {
    document.getElementById('brandingText').value = text;
    document.getElementById('brandingText').dispatchEvent(new Event('input'));
}

// 4. Generate PDF
function downloadMagnetSheet() {
    generateSheet().then(() => {
        console.log('PDF downloaded successfully');
    }).catch(error => {
        console.error('PDF generation failed:', error);
    });
}
```

---

## Programmatic Control

### Automated Image Loading

```javascript
// Load image from URL
function loadImageFromURL(imageUrl) {
    return new Promise((resolve, reject) => {
        const img = new Image();
        img.crossOrigin = 'anonymous'; // For CORS
        
        img.onload = function() {
            uploadedImage = img;
            drawTemplate();
            resolve(img);
        };
        
        img.onerror = function() {
            reject(new Error('Failed to load image'));
        };
        
        img.src = imageUrl;
    });
}

// Usage
loadImageFromURL('https://example.com/logo.png')
    .then(() => console.log('Image loaded'))
    .catch(error => console.error('Error:', error));
```

### Batch Processing

```javascript
// Process multiple designs
async function createMultipleDesigns(designs) {
    const results = [];
    
    for (const design of designs) {
        try {
            // Load image
            await loadImageFromURL(design.imageUrl);
            
            // Set branding text
            setBrandingText(design.text);
            
            // Position image
            imageX = design.position?.x || 0;
            imageY = design.position?.y || 0;
            drawTemplate();
            
            // Generate PDF
            await generateSheet();
            
            results.push({ success: true, design: design.name });
        } catch (error) {
            results.push({ success: false, design: design.name, error });
        }
    }
    
    return results;
}

// Usage
const designs = [
    {
        name: 'Design 1',
        imageUrl: 'https://example.com/image1.jpg',
        text: 'Company A',
        position: { x: 10, y: 10 }
    },
    {
        name: 'Design 2',
        imageUrl: 'https://example.com/image2.jpg',
        text: 'Company B',
        position: { x: -20, y: 15 }
    }
];

createMultipleDesigns(designs).then(results => {
    console.log('Batch processing complete:', results);
});
```

---

## Custom Workflows

### Guided Design Process

```javascript
class MagnetDesignWizard {
    constructor() {
        this.currentStep = 1;
        this.maxSteps = 4;
        this.design = {};
    }
    
    // Step 1: Image Upload
    async uploadImage(file) {
        return new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.onload = (e) => {
                const img = new Image();
                img.onload = () => {
                    uploadedImage = img;
                    this.design.image = img;
                    drawTemplate();
                    this.nextStep();
                    resolve();
                };
                img.src = e.target.result;
            };
            reader.onerror = reject;
            reader.readAsDataURL(file);
        });
    }
    
    // Step 2: Position Image
    setImagePosition(x, y) {
        imageX = x;
        imageY = y;
        this.design.position = { x, y };
        drawTemplate();
        this.nextStep();
    }
    
    // Step 3: Add Branding
    setBranding(text) {
        setBrandingText(text);
        this.design.branding = text;
        this.nextStep();
    }
    
    // Step 4: Generate PDF
    async finish() {
        await generateSheet();
        this.design.completed = true;
        return this.design;
    }
    
    nextStep() {
        if (this.currentStep < this.maxSteps) {
            this.currentStep++;
            this.updateUI();
        }
    }
    
    updateUI() {
        // Update step indicator, enable/disable buttons, etc.
        document.querySelectorAll('.step').forEach((step, index) => {
            step.classList.toggle('active', index + 1 === this.currentStep);
            step.classList.toggle('completed', index + 1 < this.currentStep);
        });
    }
}

// Usage
const wizard = new MagnetDesignWizard();

// Hook up to UI events
document.getElementById('imageLoader').addEventListener('change', (e) => {
    if (e.target.files[0]) {
        wizard.uploadImage(e.target.files[0]);
    }
});
```

### Preview Mode

```javascript
// Create preview thumbnails
function generatePreview(scale = 0.3) {
    const previewCanvas = document.createElement('canvas');
    const previewCtx = previewCanvas.getContext('2d');
    
    previewCanvas.width = bleedSize * scale;
    previewCanvas.height = bleedSize * scale;
    
    // Scale down the current design
    previewCtx.scale(scale, scale);
    
    // Draw scaled version
    if (uploadedImage) {
        previewCtx.drawImage(uploadedImage, imageX, imageY, bleedSize, bleedSize);
    }
    
    if (brandingText) {
        previewCtx.fillStyle = "black";
        previewCtx.font = "30px sans-serif";
        previewCtx.textAlign = "center";
        previewCtx.fillText(brandingText, bleedSize / 2, bleedSize - 20);
    }
    
    return previewCanvas.toDataURL();
}

// Show multiple previews
function showPreviewGrid() {
    const previewContainer = document.getElementById('previews');
    previewContainer.innerHTML = '';
    
    const variations = [
        { x: 0, y: 0, text: 'Original' },
        { x: -50, y: -50, text: 'Shifted Left' },
        { x: 50, y: 50, text: 'Shifted Right' },
        { x: 0, y: 0, text: 'No Text' }
    ];
    
    variations.forEach((variation, index) => {
        // Temporarily apply variation
        const originalX = imageX;
        const originalY = imageY;
        const originalText = brandingText;
        
        imageX = variation.x;
        imageY = variation.y;
        if (variation.text === 'No Text') brandingText = '';
        
        drawTemplate();
        
        // Create preview
        const previewImg = document.createElement('img');
        previewImg.src = generatePreview();
        previewImg.classList.add('preview-thumb');
        previewImg.onclick = () => applyVariation(variation);
        
        previewContainer.appendChild(previewImg);
        
        // Restore original
        imageX = originalX;
        imageY = originalY;
        brandingText = originalText;
    });
    
    drawTemplate(); // Restore original view
}
```

---

## Advanced Customizations

### Custom PDF Layouts

```javascript
// Create custom sheet layouts
function generateCustomSheet(layout) {
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF({
        unit: layout.unit || "in",
        format: layout.format || "letter"
    });
    
    const imgData = canvas.toDataURL("image/png");
    
    for (let row = 0; row < layout.rows; row++) {
        for (let col = 0; col < layout.cols; col++) {
            const x = layout.marginX + col * (layout.magnetSize + layout.gapX);
            const y = layout.marginY + row * (layout.magnetSize + layout.gapY);
            
            doc.addImage(imgData, "PNG", x, y, layout.magnetSize, layout.magnetSize);
        }
    }
    
    doc.save(layout.filename || "custom_sheet.pdf");
}

// Predefined layouts
const layouts = {
    standard: {
        rows: 4, cols: 3,
        magnetSize: 2.25,
        gapX: 0.2, gapY: 0.2,
        marginX: 0.5, marginY: 0.5,
        filename: "standard_sheet.pdf"
    },
    dense: {
        rows: 5, cols: 4,
        magnetSize: 1.8,
        gapX: 0.1, gapY: 0.1,
        marginX: 0.3, marginY: 0.3,
        filename: "dense_sheet.pdf"
    },
    large: {
        rows: 2, cols: 2,
        magnetSize: 3.5,
        gapX: 0.5, gapY: 0.5,
        marginX: 1.0, marginY: 1.0,
        filename: "large_sheet.pdf"
    }
};

// Usage
generateCustomSheet(layouts.dense);
```

### Image Effects

```javascript
// Add image filters and effects
function applyImageEffect(effect) {
    if (!uploadedImage) return;
    
    // Create temporary canvas for processing
    const tempCanvas = document.createElement('canvas');
    const tempCtx = tempCanvas.getContext('2d');
    tempCanvas.width = uploadedImage.width;
    tempCanvas.height = uploadedImage.height;
    
    // Draw original image
    tempCtx.drawImage(uploadedImage, 0, 0);
    
    // Apply effects
    switch (effect) {
        case 'grayscale':
            tempCtx.filter = 'grayscale(100%)';
            break;
        case 'sepia':
            tempCtx.filter = 'sepia(100%)';
            break;
        case 'blur':
            tempCtx.filter = 'blur(2px)';
            break;
        case 'brightness':
            tempCtx.filter = 'brightness(120%)';
            break;
        case 'contrast':
            tempCtx.filter = 'contrast(150%)';
            break;
    }
    
    // Create new image with effect
    const effectImage = new Image();
    effectImage.onload = () => {
        uploadedImage = effectImage;
        drawTemplate();
    };
    effectImage.src = tempCanvas.toDataURL();
}

// Image transformation utilities
function rotateImage(degrees) {
    if (!uploadedImage) return;
    
    const tempCanvas = document.createElement('canvas');
    const tempCtx = tempCanvas.getContext('2d');
    
    // Set canvas size to accommodate rotation
    const size = Math.max(uploadedImage.width, uploadedImage.height);
    tempCanvas.width = size;
    tempCanvas.height = size;
    
    // Rotate and draw
    tempCtx.translate(size / 2, size / 2);
    tempCtx.rotate(degrees * Math.PI / 180);
    tempCtx.drawImage(uploadedImage, -uploadedImage.width / 2, -uploadedImage.height / 2);
    
    // Create rotated image
    const rotatedImage = new Image();
    rotatedImage.onload = () => {
        uploadedImage = rotatedImage;
        drawTemplate();
    };
    rotatedImage.src = tempCanvas.toDataURL();
}
```

---

## Event Handling

### Custom Event System

```javascript
// Event emitter for magnet designer
class MagnetDesignerEvents extends EventTarget {
    emit(eventName, data) {
        this.dispatchEvent(new CustomEvent(eventName, { detail: data }));
    }
}

const designerEvents = new MagnetDesignerEvents();

// Enhanced functions with events
function drawTemplateWithEvents() {
    drawTemplate();
    designerEvents.emit('templateDrawn', {
        hasImage: !!uploadedImage,
        hasText: !!brandingText,
        position: { x: imageX, y: imageY }
    });
}

function setBrandingTextWithEvents(text) {
    const oldText = brandingText;
    setBrandingText(text);
    designerEvents.emit('textChanged', {
        oldText,
        newText: text
    });
}

// Listen for events
designerEvents.addEventListener('templateDrawn', (e) => {
    console.log('Template updated:', e.detail);
    updateUI(e.detail);
});

designerEvents.addEventListener('textChanged', (e) => {
    console.log('Text changed from', e.detail.oldText, 'to', e.detail.newText);
});

// Progress tracking
designerEvents.addEventListener('pdfGenerationStart', () => {
    showProgressBar();
});

designerEvents.addEventListener('pdfGenerationComplete', (e) => {
    hideProgressBar();
    showSuccessMessage(`PDF saved: ${e.detail.filename}`);
});
```

### Keyboard Shortcuts

```javascript
// Keyboard shortcuts for power users
document.addEventListener('keydown', (e) => {
    // Only handle shortcuts when canvas has focus or no input is focused
    if (document.activeElement.tagName === 'INPUT') return;
    
    switch (e.code) {
        case 'KeyD':
            if (e.ctrlKey || e.metaKey) {
                e.preventDefault();
                toggleDarkMode();
            }
            break;
            
        case 'KeyC':
            if (e.ctrlKey || e.metaKey) {
                e.preventDefault();
                toggleCutMarks();
            }
            break;
            
        case 'KeyS':
            if (e.ctrlKey || e.metaKey) {
                e.preventDefault();
                generateSheet();
            }
            break;
            
        case 'ArrowLeft':
            if (uploadedImage) {
                imageX -= e.shiftKey ? 10 : 1;
                drawTemplate();
            }
            break;
            
        case 'ArrowRight':
            if (uploadedImage) {
                imageX += e.shiftKey ? 10 : 1;
                drawTemplate();
            }
            break;
            
        case 'ArrowUp':
            if (uploadedImage) {
                imageY -= e.shiftKey ? 10 : 1;
                drawTemplate();
            }
            break;
            
        case 'ArrowDown':
            if (uploadedImage) {
                imageY += e.shiftKey ? 10 : 1;
                drawTemplate();
            }
            break;
    }
});
```

---

## Error Handling

### Comprehensive Error Handling

```javascript
// Robust error handling for all operations
class MagnetDesignerError extends Error {
    constructor(message, code, details = {}) {
        super(message);
        this.name = 'MagnetDesignerError';
        this.code = code;
        this.details = details;
    }
}

// Wrapper functions with error handling
async function safeImageUpload(file) {
    try {
        // Validate file
        if (!file) {
            throw new MagnetDesignerError('No file provided', 'NO_FILE');
        }
        
        if (!file.type.startsWith('image/')) {
            throw new MagnetDesignerError('Invalid file type', 'INVALID_TYPE', {
                providedType: file.type,
                expectedTypes: ['image/jpeg', 'image/png', 'image/gif']
            });
        }
        
        if (file.size > 10 * 1024 * 1024) { // 10MB limit
            throw new MagnetDesignerError('File too large', 'FILE_TOO_LARGE', {
                size: file.size,
                maxSize: 10 * 1024 * 1024
            });
        }
        
        // Process file
        const result = await new Promise((resolve, reject) => {
            const reader = new FileReader();
            
            reader.onload = (e) => {
                const img = new Image();
                img.onload = () => {
                    uploadedImage = img;
                    drawTemplate();
                    resolve({ success: true, image: img });
                };
                img.onerror = () => {
                    reject(new MagnetDesignerError('Failed to load image', 'IMAGE_LOAD_ERROR'));
                };
                img.src = e.target.result;
            };
            
            reader.onerror = () => {
                reject(new MagnetDesignerError('Failed to read file', 'FILE_READ_ERROR'));
            };
            
            reader.readAsDataURL(file);
        });
        
        return result;
        
    } catch (error) {
        console.error('Image upload failed:', error);
        showErrorMessage(error.message);
        throw error;
    }
}

async function safePDFGeneration() {
    try {
        // Check dependencies
        if (typeof window.jspdf === 'undefined') {
            throw new MagnetDesignerError('jsPDF library not loaded', 'MISSING_DEPENDENCY');
        }
        
        // Check canvas state
        if (!canvas || !ctx) {
            throw new MagnetDesignerError('Canvas not initialized', 'CANVAS_ERROR');
        }
        
        // Generate PDF with progress tracking
        showProgressBar();
        await generateSheet();
        hideProgressBar();
        
        return { success: true };
        
    } catch (error) {
        hideProgressBar();
        console.error('PDF generation failed:', error);
        showErrorMessage('Failed to generate PDF: ' + error.message);
        throw error;
    }
}

// Error display functions
function showErrorMessage(message) {
    const errorDiv = document.createElement('div');
    errorDiv.className = 'error-message';
    errorDiv.textContent = message;
    errorDiv.style.cssText = `
        position: fixed;
        top: 20px;
        right: 20px;
        background: #ff4444;
        color: white;
        padding: 15px;
        border-radius: 5px;
        z-index: 1000;
    `;
    
    document.body.appendChild(errorDiv);
    
    setTimeout(() => {
        errorDiv.remove();
    }, 5000);
}

function showProgressBar() {
    const progressDiv = document.createElement('div');
    progressDiv.id = 'progress-bar';
    progressDiv.innerHTML = `
        <div class="progress-container">
            <div class="progress-text">Generating PDF...</div>
            <div class="progress-bar">
                <div class="progress-fill"></div>
            </div>
        </div>
    `;
    document.body.appendChild(progressDiv);
}

function hideProgressBar() {
    const progressBar = document.getElementById('progress-bar');
    if (progressBar) {
        progressBar.remove();
    }
}
```

---

## Performance Optimization

### Optimized Rendering

```javascript
// Debounced drawing for better performance
function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

// Optimized draw function
const debouncedDraw = debounce(drawTemplate, 16); // ~60fps

// Use optimized version for rapid updates
canvas.addEventListener("mousemove", (e) => {
    if (isDragging && uploadedImage) {
        const rect = canvas.getBoundingClientRect();
        imageX = e.clientX - rect.left - bleedSize / 2;
        imageY = e.clientY - rect.top - bleedSize / 2;
        debouncedDraw(); // Use debounced version
    }
});

// Image caching for better performance
const imageCache = new Map();

async function loadAndCacheImage(url) {
    if (imageCache.has(url)) {
        return imageCache.get(url);
    }
    
    const img = await loadImageFromURL(url);
    imageCache.set(url, img);
    return img;
}

// Memory management
function clearImageCache() {
    imageCache.clear();
}

// Call periodically to prevent memory leaks
setInterval(clearImageCache, 300000); // Clear every 5 minutes
```

These examples demonstrate comprehensive usage patterns for the 2x2 Magnet Layout App, from basic integration to advanced customizations. Each example includes error handling, performance considerations, and practical implementation details that developers can adapt for their specific needs.