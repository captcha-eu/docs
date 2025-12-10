# Content Security Policy (CSP) Integration Guide

## Overview

Captcha.eu works with Content Security Policy (CSP) headers. This guide shows you the **practical** CSP configuration that balances security with compatibility.

## Recommended CSP Configuration

The recommended CSP configuration scopes permissions specifically to `captcha.eu`:

```
Content-Security-Policy:
  script-src 'self' https://www.captcha.eu 'unsafe-inline';
  style-src 'self' https://www.captcha.eu 'unsafe-inline';
  worker-src blob:;
  connect-src https://www.captcha.eu;
  frame-src 'self';
  img-src 'self' https://www.captcha.eu data:;
```

**Key Security Features:**
- ✅ External scripts only from `captcha.eu` (no other third-party scripts allowed)
- ✅ API calls only to `captcha.eu` (no data leaks to other domains)
- ✅ Workers use blob: URLs from fetched trusted source (Firefox-compatible)
- ⚠️ Requires `'unsafe-inline'` for scripts and styles (widget needs inline execution)

**Why This Is Secure:**
Even with `'unsafe-inline'`, your CSP is still very restrictive:
- **Scripts** can only load from your domain and `captcha.eu`
- **Network requests** can only go to your domain and `captcha.eu`
- **Workers** use blob: URLs created from scripts fetched from `captcha.eu`
- All permissions are domain-scoped (no wildcards)

### Understanding Each Directive

| Directive | Value | Why Required |
|-----------|-------|--------------|
| `script-src` | `'self' https://www.captcha.eu 'unsafe-inline'` | Load SDK and execute initialization code |
| `style-src` | `'self' https://www.captcha.eu 'unsafe-inline'` | Widget styling and animations |
| `worker-src` | `blob:` | Background challenge processing (see note below) |
| `connect-src` | `https://www.captcha.eu` | API calls for challenges and validation |
| `frame-src` | `'self'` | V2 widget iframe rendering |
| `img-src` | `'self' https://www.captcha.eu data:` | Logo and challenge images |

### Why `worker-src blob:` Instead of a Domain?

**Firefox does not support cross-origin Web Workers**, even with proper CORS headers. This is per the HTML Living Standard specification.

**Browser behavior:**
| Browser | `new Worker("https://captcha.eu/worker.js")` | Result |
|---------|---------------------------------------------|--------|
| Chrome | ✅ Works with CORS headers | Direct loading |
| Firefox | ❌ SecurityError | Always blocked |
| Safari | ❌ SecurityError | Always blocked |

**Our solution (blob pattern):**
1. `fetch()` the worker script from `captcha.eu` (respects CORS ✅)
2. Create a `Blob` from the script content
3. Create a blob URL via `URL.createObjectURL()`
4. `new Worker(blobURL)` works because blob URLs are same-origin

This is the **industry-standard workaround** used by Monaco Editor, Ace Editor, and other major projects.

**Security:** The `blob:` directive is safe because:
- The script content is still fetched from `captcha.eu` with CORS verification
- `connect-src https://www.captcha.eu` ensures only captcha.eu can be fetched
- No arbitrary code can be executed - only verified captcha.eu scripts

## Implementation Examples

### Nginx

```nginx
location / {
    add_header Content-Security-Policy "script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src blob:; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;" always;
}
```

### Apache

```apache
<IfModule mod_headers.c>
    Header always set Content-Security-Policy "script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src blob:; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;"
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
      workerSrc: ["blob:"],
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
      "worker-src blob:",
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
CSP_WORKER_SRC = ("blob:",)
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
  content="script-src 'self' https://www.captcha.eu 'unsafe-inline'; style-src 'self' https://www.captcha.eu 'unsafe-inline'; worker-src blob:; connect-src https://www.captcha.eu; frame-src 'self'; img-src 'self' https://www.captcha.eu data:;"
/>
```

## Troubleshooting

### Widget Doesn't Load

**Symptom:** Widget container is empty, no errors in console

**Solution:** Verify all required CSP directives are present:
- Check `script-src` includes `https://www.captcha.eu 'unsafe-inline'`
- Check `connect-src` includes `https://www.captcha.eu`

### CSP Violations for Inline Styles

**Symptom:** Console shows `style-src` violations

**Solution:**
- Ensure `'unsafe-inline'` is in `style-src` directive

### Worker Loading Errors

**Symptom:** Console shows worker loading failures or SecurityError

**Solution:**
- Verify `worker-src blob:` is in your CSP
- Ensure `connect-src https://www.captcha.eu` is present (needed to fetch the worker script)
- If you see "SecurityError: cross-origin worker" in Firefox, this is expected - our SDK handles it automatically with the blob pattern

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
Content-Security-Policy-Report-Only: script-src 'self' https://www.captcha.eu 'unsafe-inline';
```

This will log violations without blocking content, allowing you to test safely.

### 3. Live Demo

Check out our live demo page to see CSP in action:

```
https://www.captcha.eu/api/csp_demo/overview
```

## On-Premise / Custom Domain

If you're using Captcha.eu on a custom domain or on-premise installation, replace `https://www.captcha.eu` with your custom domain in all CSP directives (except `worker-src` which always uses `blob:`):

```
Content-Security-Policy:
  script-src 'self' https://captcha.yourdomain.com 'unsafe-inline';
  style-src 'self' https://captcha.yourdomain.com 'unsafe-inline';
  worker-src blob:;
  connect-src https://captcha.yourdomain.com;
  ...
```

## Security Best Practices

### 1. Use Domain Scoping

Always scope permissions to the specific domain:

```
✅ Good: script-src 'self' https://www.captcha.eu
❌ Bad:  script-src 'self' https://*.eu
```

### 2. Avoid Wildcards

Never use `*` in CSP directives - always specify exact domains:

```
❌ Bad: script-src *
✅ Good: script-src 'self' https://www.captcha.eu
```

### 3. Test in Staging First

Always test CSP changes in a staging environment before deploying to production.

### 4. Monitor for Violations

Set up CSP violation reporting to catch issues:

```javascript
document.addEventListener('securitypolicyviolation', (e) => {
  console.error('CSP Violation:', {
    violatedDirective: e.violatedDirective,
    blockedURI: e.blockedURI,
    originalPolicy: e.originalPolicy
  });
});
```

## Compatibility Matrix

| CSP Feature | Supported | Notes |
|------------|-----------|-------|
| script-src | ✅ | Requires `https://www.captcha.eu 'unsafe-inline'` |
| style-src | ✅ | Requires `'unsafe-inline'` |
| worker-src | ✅ | Requires `blob:` (Firefox compatibility) |
| connect-src | ✅ | Required for API calls and worker script fetch |
| frame-src | ✅ | Required for V2 widget |
| img-src data: | ✅ | Required for images |

## Common Patterns

### WordPress with CSP Plugin

```php
// In your theme's functions.php or CSP plugin settings
add_filter('csp_directives', function($directives) {
    $directives['script-src'][] = 'https://www.captcha.eu';
    $directives['script-src'][] = "'unsafe-inline'";
    $directives['style-src'][] = 'https://www.captcha.eu';
    $directives['style-src'][] = "'unsafe-inline'";
    $directives['worker-src'][] = 'blob:';
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
                   worker-src blob:;
                   connect-src https://www.captcha.eu;
                   frame-src 'self';
                   img-src 'self' https://www.captcha.eu data:;">
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

## FAQ

### Q: Do I need `'unsafe-eval'`?

**A:** No. Captcha.eu SDK does not use `eval()` and does not require `'unsafe-eval'`.

### Q: Why do I need `'unsafe-inline'`?

**A:** The current SDK version requires inline script and style execution for widget initialization and styling. This is scoped to specific domains for security.

### Q: Is `data:` in `img-src` safe?

**A:** Yes. We use `data:` URIs for canvas-based image challenges. This is a standard pattern and poses no security risk in this context.

### Q: Can I tighten the CSP further?

**A:** Not without breaking the SDK. The directives shown are the minimum required for the SDK to function. They provide strong security through domain scoping.

### Q: What about subdomains?

**A:** Currently, only `https://www.captcha.eu` is required. If we add subdomains in the future, we'll update this documentation.

### Q: Does CSP affect performance?

**A:** No. CSP headers add negligible overhead and do not impact SDK performance.

## Support

If you encounter CSP issues not covered in this guide:

1. Check the live demo: https://www.captcha.eu/api/csp_demo/overview
2. Review browser console for specific violation messages
3. Contact support with CSP violation details

## References

- [MDN: Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [CSP Evaluator (Google)](https://csp-evaluator.withgoogle.com/)
- [Captcha.eu Widget Documentation](widget.md)
- [Captcha.eu Integration Guide](install.md)

---

**Last Updated:** 2025-12-10
**SDK Version:** Latest (with blob-based worker loading for Firefox compatibility)
