# Content Security Policy (CSP) Integration Guide

## Overview

Captcha.eu is designed to work with Content Security Policy (CSP) headers, providing strong security while maintaining compatibility with strict CSP configurations.

**Key Features:**
- ✅ No `blob:` required for workers (uses direct CORS-based loading)
- ✅ Optional nonce support for eliminating `unsafe-inline`
- ✅ Backward compatible with existing deployments

## Quick Start - Minimum Required CSP

The minimum CSP configuration needed for Captcha.eu:

```
Content-Security-Policy:
  script-src 'self' https://www.captcha.eu 'unsafe-inline';
  style-src 'self' https://www.captcha.eu 'unsafe-inline';
  worker-src https://www.captcha.eu;
  connect-src https://www.captcha.eu;
  frame-src 'self';
  img-src 'self' https://www.captcha.eu data:;
```

### Understanding Each Directive

| Directive | Value | Why Required |
|-----------|-------|--------------|
| `script-src` | `https://www.captcha.eu 'unsafe-inline'` | Load SDK and execute initialization code |
| `style-src` | `https://www.captcha.eu 'unsafe-inline'` | Widget styling (can use nonce instead) |
| `worker-src` | `https://www.captcha.eu` | Background challenge processing |
| `connect-src` | `https://www.captcha.eu` | API calls for challenges and validation |
| `frame-src` | `'self'` | V2 widget iframe rendering |
| `img-src` | `data:` | Logo and challenge images |

## Implementation Examples

### Nginx

```nginx
location / {
    add_header Content-Security-Policy "script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src https://www.captcha.eu; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;" always;
}
```

### Apache

```apache
<IfModule mod_headers.c>
    Header always set Content-Security-Policy "script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src https://www.captcha.eu; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;"
</IfModule>
```

### Node.js / Express

```javascript
const helmet = require('helmet');

app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "https://www.captcha.eu", "'unsafe-inline'"],
      styleSrc: ["'self'", "https://www.captcha.eu", "'unsafe-inline'"],
      workerSrc: ["https://www.captcha.eu"],
      connectSrc: ["https://www.captcha.eu"],
      frameSrc: ["'self'"],
      imgSrc: ["'self'", "https://www.captcha.eu", "data:"],
    },
  })
);
```

### Next.js

```javascript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: [
      "script-src 'self' https://www.captcha.eu 'unsafe-inline'",
      "style-src 'self' https://www.captcha.eu 'unsafe-inline'",
      "worker-src https://www.captcha.eu",
      "connect-src https://www.captcha.eu",
      "frame-src 'self'",
      "img-src 'self' https://www.captcha.eu data:",
    ].join('; ')
  }
];

module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: securityHeaders,
      },
    ];
  },
};
```

### Django

```python
# settings.py
CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'", "https://www.captcha.eu", "'unsafe-inline'")
CSP_STYLE_SRC = ("'self'", "https://www.captcha.eu", "'unsafe-inline'")
CSP_WORKER_SRC = ("https://www.captcha.eu",)
CSP_CONNECT_SRC = ("https://www.captcha.eu",)
CSP_FRAME_SRC = ("'self'",)
CSP_IMG_SRC = ("'self'", "https://www.captcha.eu", "data:")

MIDDLEWARE = [
    # ...
    'csp.middleware.CSPMiddleware',
    # ...
]
```

### React (Meta Tag)

```jsx
// In your HTML template or helmet configuration
<meta
  httpEquiv="Content-Security-Policy"
  content="script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src https://www.captcha.eu; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;"
/>
```

## Advanced: Strict CSP with Nonces

For maximum security, you can eliminate `'unsafe-inline'` by using cryptographic nonces.

### Step 1: Generate a Nonce

Generate a unique nonce for each page load:

**Node.js:**
```javascript
const crypto = require('crypto');
const nonce = crypto.randomBytes(16).toString('base64');
```

**PHP:**
```php
$nonce = base64_encode(random_bytes(16));
```

**Python:**
```python
import secrets
import base64
nonce = base64.b64encode(secrets.token_bytes(16)).decode('utf-8')
```

### Step 2: Add Nonce to CSP Header

```javascript
// Express example
app.use((req, res, next) => {
  const nonce = crypto.randomBytes(16).toString('base64');
  res.locals.nonce = nonce;

  res.setHeader('Content-Security-Policy',
    `script-src 'self' https://www.captcha.eu; ` +
    `style-src 'self' https://www.captcha.eu 'nonce-${nonce}'; ` +
    `worker-src https://www.captcha.eu; ` +
    `connect-src https://www.captcha.eu; ` +
    `frame-src 'self'; ` +
    `img-src 'self' https://www.captcha.eu data:;`
  );

  next();
});
```

### Step 3: Pass Nonce to SDK

Configure the SDK with your nonce:

```html
<script>
  window.CaptchaEUSettings = {
    cspNonce: '<%= nonce %>',  // Server-generated nonce
    publicSecret: 'YOUR_PUBLIC_KEY'
  };
</script>
<script src="https://www.captcha.eu/sdk/captcha.js"></script>
```

The SDK will automatically apply the nonce to all dynamically created style elements.

## Troubleshooting

### Widget Doesn't Load

**Symptom:** Widget container is empty, no errors in console

**Solution:** Verify all required CSP directives are present:
- Check `script-src` includes `https://www.captcha.eu`
- Check `connect-src` includes `https://www.captcha.eu`

### CSP Violations for Inline Styles

**Symptom:** Console shows `style-src` violations

**Solution:**
- Ensure `'unsafe-inline'` is in `style-src` directive, OR
- Implement nonce-based CSP (see Advanced section above)

### Worker Loading Errors

**Symptom:** Console shows worker loading failures

**Solution:**
- Verify `worker-src https://www.captcha.eu` is in your CSP
- DO NOT include `blob:` - it's not needed!

### Images Not Loading

**Symptom:** Logo or challenge images don't display

**Solution:**
- Add `data:` to `img-src` directive
- Ensure `https://www.captcha.eu` is in `img-src`

### V2 Widget Iframe Issues

**Symptom:** V2 widget doesn't render

**Solution:**
- Verify `frame-src 'self'` is in your CSP
- If using cross-origin frames, adjust accordingly

## Testing Your CSP Configuration

### 1. Browser Console Check

Open your browser's developer console (F12) and look for:
- ✅ No CSP violation warnings
- ✅ Widget loads and renders correctly
- ✅ Challenges complete successfully

### 2. CSP Report-Only Mode

Test your CSP without blocking by using report-only mode:

```
Content-Security-Policy-Report-Only: script-src 'self' https://www.captcha.eu;
```

This will log violations without blocking content, allowing you to test safely.

### 3. Debug Mode

Enable debug mode to see detailed CSP status:

```
https://your-site.com/?cpt_debug=true
```

Check console for messages like:
```
[CSP] Direct worker loading successful - using strict CSP mode
```

## On-Premise / Custom Domain

If you're using Captcha.eu on a custom domain or on-premise installation, replace `https://www.captcha.eu` with your custom domain in all CSP directives:

```
Content-Security-Policy:
  script-src 'self' https://captcha.yourdomain.com 'unsafe-inline';
  style-src 'self' https://captcha.yourdomain.com 'unsafe-inline';
  worker-src https://captcha.yourdomain.com;
  connect-src https://captcha.yourdomain.com;
  ...
```

## Security Best Practices

### 1. Use Nonces When Possible

Nonces provide the strongest CSP protection by eliminating `'unsafe-inline'`:

```
style-src 'self' https://www.captcha.eu 'nonce-{random}'
```

### 2. Avoid Wildcards

Never use `*` in CSP directives - always specify exact domains:

❌ Bad: `script-src *`
✅ Good: `script-src 'self' https://www.captcha.eu`

### 3. No blob: Required

Modern Captcha.eu SDK doesn't require `blob:` for workers:

❌ Old: `worker-src https://www.captcha.eu blob:`
✅ New: `worker-src https://www.captcha.eu`

### 4. Generate Fresh Nonces

Always generate a new nonce for each page load:

```javascript
// ✅ Good - new nonce per request
const nonce = crypto.randomBytes(16).toString('base64');

// ❌ Bad - reused nonce
const nonce = 'static-value-12345';
```

### 5. Test in Staging First

Always test CSP changes in a staging environment before deploying to production.

## Compatibility Matrix

| CSP Feature | Supported | Notes |
|------------|-----------|-------|
| script-src | ✅ | Requires `https://www.captcha.eu` |
| style-src with nonce | ✅ | SDK supports nonces |
| style-src unsafe-inline | ✅ | Fallback for legacy |
| worker-src direct loading | ✅ | No blob: required |
| worker-src blob (legacy) | ✅ | Backward compatible fallback |
| connect-src | ✅ | Required for API calls |
| frame-src | ✅ | Required for V2 widget |
| img-src data: | ✅ | Required for images |

## Migration from Legacy CSP

If you previously had `blob:` in your CSP, you can now remove it:

**Before:**
```
worker-src https://www.captcha.eu blob:;
```

**After:**
```
worker-src https://www.captcha.eu;
```

The SDK will automatically use direct worker loading. The blob fallback is maintained for backward compatibility but won't be used with proper CORS headers.

## Common Patterns

### WordPress with CSP Plugin

```php
// In your theme's functions.php or CSP plugin settings
add_filter('csp_directives', function($directives) {
    $directives['script-src'][] = 'https://www.captcha.eu';
    $directives['script-src'][] = "'unsafe-inline'";
    $directives['style-src'][] = 'https://www.captcha.eu';
    $directives['style-src'][] = "'unsafe-inline'";
    $directives['worker-src'][] = 'https://www.captcha.eu';
    $directives['connect-src'][] = 'https://www.captcha.eu';
    $directives['img-src'][] = 'https://www.captcha.eu';
    $directives['img-src'][] = 'data:';
    return $directives;
});
```

### Single Page Applications (SPA)

For React, Vue, Angular, etc., set CSP via meta tag or server headers:

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Security-Policy"
          content="script-src 'self' https://www.captcha.eu 'unsafe-inline';
                   style-src 'self' https://www.captcha.eu 'unsafe-inline';
                   worker-src https://www.captcha.eu;
                   connect-src https://www.captcha.eu;
                   frame-src 'self';
                   img-src 'self' https://www.captcha.eu data:;">
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

## Support

If you encounter CSP-related issues:

1. Check browser console for specific CSP violation messages
2. Enable debug mode: `?cpt_debug=true`
3. Review this guide's troubleshooting section
4. Contact support: [https://www.captcha.eu/contact](https://www.captcha.eu/contact)

## Further Reading

- [MDN: Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [CSP Evaluator](https://csp-evaluator.withgoogle.com/)
- [Captcha.eu Widget Documentation](widget.md)
- [Captcha.eu Integration Guide](install.md)

---

**Last Updated:** 2025-11-26
**SDK Version:** Latest (with CORS worker loading)
