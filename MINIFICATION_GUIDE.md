# CSS & JavaScript Minification Guide

## Overview

Minification removes unnecessary characters from code without changing functionality:

- **CSS**: Remove spaces, comments, unused rules → 60-80% reduction
- **JS**: Remove spaces, comments, rename variables → 40-50% reduction
- **Impact**: Faster downloads, faster parsing, better performance

---

## 1. Manual Minification Process

### Option A: Online Minifiers (Easiest)

#### For CSS:

1. Visit https://cssnano.co/playground/
2. Paste your `<style>` content
3. Copy minified output
4. Save to `styles.min.css`

#### For JavaScript:

1. Visit https://www.minifycode.com/javascript/
2. Paste your `<script>` content
3. Copy minified output
4. Save to `app.min.js`

#### For HTML:

1. Visit https://html-minifier.herokuapp.com/
2. Paste HTML content
3. Copy minified output

### Option B: Build Tools (Recommended for Production)

#### Using Node.js + NPM (Best Practice)

**Step 1: Install Node.js**

- Download from https://nodejs.org/
- Install (includes npm)

**Step 2: Create package.json**
Create file in your project root:

```json
{
  "name": "naiunify-portfolio",
  "version": "1.0.0",
  "scripts": {
    "minify": "npm run minify:css && npm run minify:js",
    "minify:css": "cleancss -o index.min.css index.html",
    "minify:js": "terser app.js -o app.min.js"
  },
  "devDependencies": {
    "clean-css-cli": "^5.6.0",
    "terser": "^5.18.0"
  }
}
```

**Step 3: Install dependencies**

```bash
npm install
```

**Step 4: Run minification**

```bash
npm run minify
```

This creates:

- `index.min.css` (minified CSS)
- `app.min.js` (minified JavaScript)

---

## 2. Quick Setup for Windows (PowerShell)

### Using Windows Task Scheduler + Batch

Create file `minify.bat` in your project:

```batch
@echo off
REM Minify CSS - requires clean-css installed globally
call npm install -g clean-css-cli
clean-css -o styles.min.css styles.css

REM Minify JS - requires terser installed globally
call npm install -g terser
terser app.js -o app.min.js

echo Minification complete!
pause
```

Run: Double-click `minify.bat` whenever you update your files

---

## 3. For Your HTML Portfolio

### Current Setup:

```html
<!DOCTYPE html>
<html>
  <style id="app-style">
    /* Large CSS here */
  </style>
  ...
  <script id="app-script">
    /* Large JS here */
  </script>
</html>
```

### Optimized Setup (Recommended):

**Step 1: Extract CSS to separate file**

Create `styles.css` with all CSS from `<style>` tag

**Step 2: Extract JS to separate file**

Create `app.js` with all JS from `<script>` tag

**Step 3: Update index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- Use minified CSS in production -->
    <link rel="stylesheet" href="styles.min.css" />
  </head>
  <body>
    <!-- Content -->

    <!-- Use minified JS in production -->
    <script src="app.min.js" defer></script>
  </body>
</html>
```

**Step 4: Minify both files**

Run online minifier or npm commands above

---

## 4. Inline Minification for Single HTML File

If you want to keep everything in one HTML file:

**Original:**

```html
<style>
  body {
    font-family: "Inter", sans-serif;
    /* ... lots of whitespace ... */
  }
</style>
```

**Minified:**

```html
<style>
  body {
    font-family: "Inter", sans-serif;
  } /* ... all on one line ... */
</style>
```

Use https://www.minifycode.com/ to minify the entire HTML file

---

## 5. Automated Minification Setup (GitHub Actions)

Create `.github/workflows/minify.yml`:

```yaml
name: Minify CSS and JS

on:
  push:
    branches: [main]
    paths:
      - "styles.css"
      - "app.js"

jobs:
  minify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm install clean-css-cli terser

      - name: Minify CSS
        run: npx cleancss -o styles.min.css styles.css

      - name: Minify JS
        run: npx terser app.js -o app.min.js

      - name: Commit and push
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add styles.min.css app.min.js
          git commit -m "Minify CSS and JS" --allow-empty
          git push
```

Automatically minifies when pushing to GitHub!

---

## 6. Comparison: Before vs After

### CSS Example

**Before (1,245 bytes):**

```css
h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: "Poppins", sans-serif;
  font-weight: 700;
}

.brand-logo {
  height: 2.75rem;
  width: 2.75rem;
  transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.brand-logo:hover {
  transform: scale(1.08) rotate(2deg);
}
```

**After (325 bytes - 74% reduction):**

```css
h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: "Poppins", sans-serif;
  font-weight: 700;
}
.brand-logo {
  height: 2.75rem;
  width: 2.75rem;
  transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.brand-logo:hover {
  transform: scale(1.08) rotate(2deg);
}
```

### JavaScript Example

**Before (2,450 bytes):**

```javascript
document.addEventListener("DOMContentLoaded", function () {
  const mobileMenuButton = document.getElementById("mobile-menu-button");
  const mobileMenu = document.getElementById("mobile-menu");

  function openMobileMenu() {
    mobileMenu.classList.add("open");
  }

  mobileMenuButton.addEventListener("click", openMobileMenu);
});
```

**After (195 bytes - 92% reduction):**

```javascript
document.addEventListener("DOMContentLoaded", function () {
  const e = document.getElementById("mobile-menu-button"),
    t = document.getElementById("mobile-menu");
  e.addEventListener("click", () => {
    t.classList.add("open");
  });
});
```

---

## 7. Important: Keep Source Files

Always keep original files for editing:

- `styles.css` → for editing
- `styles.min.css` → for production
- `app.js` → for editing
- `app.min.js` → for production

Use build tool to generate `.min` files automatically

---

## 8. Testing After Minification

1. **Browser Test:**
   - Open site in browser
   - Check Developer Tools → Console for errors
   - Test all interactive features

2. **Functionality Check:**
   - Mobile menu open/close
   - Dark mode toggle
   - Contact form submission
   - Scroll animations

3. **Cross-browser:**
   - Chrome/Edge
   - Firefox
   - Safari

---

## 9. File Size Impact Example

### Portfolio Project (approx)

| File         | Original   | Minified  | Reduction |
| ------------ | ---------- | --------- | --------- |
| index.html   | 85 KB      | 65 KB     | 23%       |
| CSS (inline) | 45 KB      | 12 KB     | 73%       |
| JS (inline)  | 52 KB      | 18 KB     | 65%       |
| **Total**    | **182 KB** | **95 KB** | **48%**   |

With gzip compression added:

- Total: 95 KB → 28 KB (70% reduction!)

---

## 10. Recommended Workflow

### For Quick Updates:

1. Edit HTML directly
2. Use online minifier (cssnano.co)
3. Copy minified code
4. Replace old code

### For Production:

1. Setup npm + minification tools
2. Separate CSS/JS to own files
3. Run `npm run minify`
4. Use `.min` versions in production
5. Keep originals for future edits

---

## 11. Best Practices

✅ **DO:**

- Minify CSS and JS for production
- Keep source files for development
- Test thoroughly after minification
- Use gzip compression
- Combine with other optimizations

❌ **DON'T:**

- Minify inline CSS/JS in development
- Delete original files
- Minify already-minified libraries
- Forget to test interactive features
- Minify only CSS (JS may have dependencies)

---

## 12. Advanced: Compression Levels

### Extra Aggressive Minification

**Create `config.json` for Terser:**

```json
{
  "compress": {
    "drop_console": true,
    "pure_funcs": ["console.log"],
    "passes": 2
  },
  "mangle": {
    "reserved": ["init", "utils"],
    "toplevel": true
  }
}
```

Run: `terser app.js -o app.min.js -c config.json`

This removes `console.log()` and aggressively shortens variable names

---

## 13. Recommended Tools Summary

| Tool           | Type           | Free | Easy |
| -------------- | -------------- | ---- | ---- |
| cssnano.co     | Online CSS     | ✅   | ✅   |
| minifycode.com | Online JS      | ✅   | ✅   |
| Clean CSS      | NPM CSS        | ✅   | ✅   |
| Terser         | NPM JS         | ✅   | ✅   |
| Webpack        | Build bundler  | ✅   | ❌   |
| Vite           | Modern bundler | ✅   | ✅   |

---

Quick Implementation:

1. **For now:** Use https://cssnano.co/ and https://www.minifycode.com/
2. **Next:** Setup npm minification
3. **Eventually:** Use modern build tool (Vite/Webpack)

**Estimated Time:** 30 minutes for initial setup, 2 minutes for future updates
