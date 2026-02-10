# Browser Caching & Performance Headers Guide

## Overview

Cache-Control headers tell browsers how long to store files locally, reducing server requests and improving load times. This is critical for performance optimization.

---

## 1. Caching Strategy by File Type

### Static Assets (Images, Fonts) - LONG TERM

Cache for 1 year (aggressive caching safe for versioned files):

```apache
<FilesMatch "\.(jpg|jpeg|png|gif|ico|webp|svg)$">
  Header set Cache-Control "public, max-age=31536000, immutable"
  Header set Expires "Thu, 31 Dec 2099 23:59:59 GMT"
</FilesMatch>

<FilesMatch "\.(woff|woff2|ttf|otf|eot)$">
  Header set Cache-Control "public, max-age=31536000, immutable"
  Header set Access-Control-Allow-Origin "*"
</FilesMatch>
```

### CSS & JavaScript - MEDIUM TERM

Cache for 30 days (use versioning in filenames):

```apache
<FilesMatch "\.(js|css)$">
  Header set Cache-Control "public, max-age=2592000"
</FilesMatch>
```

### HTML Files - SHORT TERM

Cache for 1 hour (always check for updates):

```apache
<FilesMatch "\.html?$">
  Header set Cache-Control "public, max-age=3600"
  Header set Expires "now"
</FilesMatch>
```

### API Responses - NO CACHE

Never cache dynamic content:

```apache
<FilesMatch "^api\/" >
  Header set Cache-Control "no-store, must-revalidate"
  Header set Pragma "no-cache"
</FilesMatch>
```

---

## 2. Complete .htaccess Configuration

Place this in your website root directory:

```apache
# Enable compression
<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/plain
  AddOutputFilterByType DEFLATE text/html
  AddOutputFilterByType DEFLATE text/xml
  AddOutputFilterByType DEFLATE text/css
  AddOutputFilterByType DEFLATE text/javascript
  AddOutputFilterByType DEFLATE application/xml
  AddOutputFilterByType DEFLATE application/xhtml+xml
  AddOutputFilterByType DEFLATE application/rss+xml
  AddOutputFilterByType DEFLATE application/javascript
  AddOutputFilterByType DEFLATE application/x-javascript
  AddOutputFilterByType DEFLATE application/x-font-ttf
  AddOutputFilterByType DEFLATE application/font-woff
  AddOutputFilterByType DEFLATE application/font-woff2
</IfModule>

# Enable browser caching
<IfModule mod_expires.c>
  ExpiresActive On

  # Default
  ExpiresDefault "access plus 2 days"

  # Images
  ExpiresByType image/jpeg "access plus 1 year"
  ExpiresByType image/gif "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"
  ExpiresByType image/webp "access plus 1 year"
  ExpiresByType image/x-icon "access plus 1 year"
  ExpiresByType image/x-icon "access plus 1 year"

  # CSS and JavaScript
  ExpiresByType text/css "access plus 1 month"
  ExpiresByType text/javascript "access plus 1 month"
  ExpiresByType application/javascript "access plus 1 month"

  # Fonts
  ExpiresByType application/font-woff "access plus 1 year"
  ExpiresByType application/font-woff2 "access plus 1 year"
  ExpiresByType font/ttf "access plus 1 year"
  ExpiresByType font/otf "access plus 1 year"

  # HTML - Don't cache
  ExpiresByType text/html "access plus 1 hour"
</IfModule>

# Set Headers
<IfModule mod_headers.c>
  # Images, Fonts
  <FilesMatch "\.(jpg|jpeg|png|gif|ico|webp|svg|woff|woff2|ttf|otf|eot)$">
    Header set Cache-Control "public, max-age=31536000, immutable"
  </FilesMatch>

  # CSS, JS
  <FilesMatch "\.(css|js)$">
    Header set Cache-Control "public, max-age=2592000"
  </FilesMatch>

  # HTML
  <FilesMatch "\.html?$">
    Header set Cache-Control "public, max-age=3600"
  </FilesMatch>
</IfModule>

# HTTPS Redirect
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{HTTPS} off
  RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</IfModule>
```

---

## 3. For Cloud Platforms (Netlify, Vercel, Cloudflare)

### Netlify Configuration

Create `netlify.toml` in your root directory:

```toml
[[headers]]
  for = "/index.html"
  [headers.values]
    Cache-Control = "public, max-age=3600"

[[headers]]
  for = "/static/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/*.js"
  [headers.values]
    Cache-Control = "public, max-age=2592000"

[[headers]]
  for = "/*.css"
  [headers.values]
    Cache-Control = "public, max-age=2592000"

[[headers]]
  for = "/images/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/fonts/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
    Access-Control-Allow-Origin = "*"
```

### Vercel Configuration

Create `vercel.json` in your root directory:

```json
{
  "headers": [
    {
      "source": "/index.html",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=3600"
        }
      ]
    },
    {
      "source": "/static/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    },
    {
      "source": "/(.*)\\.js",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=2592000"
        }
      ]
    },
    {
      "source": "/(.*)\\.css",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=2592000"
        }
      ]
    },
    {
      "source": "/images/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    }
  ]
}
```

### Cloudflare Configuration

1. Log in to Cloudflare dashboard
2. Select your domain
3. Go to **Caching > Cache Rules**
4. Create rule: Match `*.jpg|*.png|*.gif|*.webp`
   - Set Cache TTL: 12 months
5. Create rule: Match `*.js|*.css`
   - Set Cache TTL: 30 days
6. Create rule: Match `index.html|*.html`
   - Set Cache TTL: 1 hour

---

## 4. Browser Caching Headers Explained

| Header          | Value              | Meaning                              |
| --------------- | ------------------ | ------------------------------------ |
| `Cache-Control` | `public`           | Anyone can cache                     |
|                 | `private`          | Only browser can cache               |
|                 | `max-age=31536000` | Cache for 1 year (seconds)           |
|                 | `immutable`        | Never changes, safe to cache forever |
|                 | `no-cache`         | Must revalidate before using         |
|                 | `no-store`         | Never cache                          |
| `ETag`          | Hash value         | Validates if file changed            |
| `Last-Modified` | Date               | When file was last updated           |
| `Expires`       | Date               | Old way to set expiration            |

---

## 5. Testing Your Caching

### Using Browser DevTools:

1. Open DevTools (F12) → Network tab
2. Reload page twice
3. Second load: Check "Size" column for "(from cache)"
4. Check Response Headers for Cache-Control

### Using Online Tools:

- **WebPageTest**: https://www.webpagetest.org/
- **GTmetrix**: https://gtmetrix.com/
- **Google PageSpeed**: https://pagespeed.web.dev/

### Check HTTP Headers:

```bash
# On macOS/Linux
curl -I https://yoursite.com

# Look for Cache-Control in response headers
```

---

## 6. Cache Busting Strategy

When you update CSS/JS, browsers will still serve cached old versions!

### Solution: Filename Versioning

**Current:**

```html
<link rel="stylesheet" href="styles.css" />
<script src="app.js"></script>
```

**With Version Numbers:**

```html
<link rel="stylesheet" href="styles-2024-02-08.css" />
<script src="app-2024-02-08.js"></script>
```

Or use build tools like:

- **Webpack**: Automatically adds hash: `app.abc123.js`
- **Gulp**: Task for versioning
- **Vite**: Automatic hash-based versioning

---

## 7. Performance Impact

With proper caching:

- **First visit**: Normal load time
- **Repeat visits**: 50-80% faster (files from cache)
- **Bandwidth saved**: 70-90% for returning visitors
- **Server load**: Reduced significantly

---

## Implementation Checklist

- [ ] Add `.htaccess` with caching rules (Apache)
- [ ] Or create `netlify.toml`/`vercel.json` (Cloudflare/Vercel/Netlify)
- [ ] Enable gzip compression
- [ ] Test with browser DevTools
- [ ] Verify with gtmetrix.com or webpagetest.org
- [ ] Set up cache busting for CSS/JS updates
- [ ] Monitor performance impact (Google Analytics)

---

## Resources

- [MDN: Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)
- [Google Web Fundamentals: HTTP Caching](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
- [Netlify Caching Guide](https://docs.netlify.com/routing/headers/)
- [Vercel Caching Guide](https://vercel.com/docs/edge-network/caching)

**Estimated Setup Time**: 10-20 minutes
