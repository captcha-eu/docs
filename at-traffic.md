# 🇦🇹 100% Austria Network Path

## Default vs Austria-Only Routing

By default, captcha.eu uses a global CDN to optimize performance and route traffic to our Austrian servers. This provides the best speed for users worldwide while maintaining data sovereignty in Austria.

## When to Use Austria-Only Routing

Consider the Austria-only path if you:
- Have strict data locality requirements
- Prefer direct connections to Austrian infrastructure
- Need to ensure zero CDN intermediaries
- Want to comply with specific regulatory requirements

## Implementation

To use the Austria-only network path, you need to make two changes:

### Default Endpoint (CDN-optimized)
```html
<script src="https://www.captcha.eu/sdk.js" defer></script>
<script>
  KROT.setup("YOUR-PUBLIC-KEY");
  // Uses global CDN routing
</script>
```

### Austria-Only Endpoint
```html
<script src="https://at.captcha.at/sdk.js" defer></script>
<script>
  KROT.setup("YOUR-PUBLIC-KEY");
  // Set Austria-only host AFTER setup but BEFORE getSolution()
  KROT.KROT_HOST = "https://at.captcha.at";
</script>
```

### Important Implementation Notes

1. **Load JS from Austrian domain**: Use `https://at.captcha.at/sdk.js`
2. **Set KROT_HOST after setup**: Must be called after `KROT.setup()` but before any `KROT.getSolution()` calls
3. **Order matters**: The sequence must be: load SDK → setup → set host → get solution

### Complete Working Example

```html
<!DOCTYPE html>
<html>
<head>
    <title>Austria-Only captcha.eu</title>
</head>
<body>
    <form id="myForm">
        <input type="email" name="email" required>
        <button type="submit">Submit</button>
    </form>

    <!-- Load SDK from Austrian domain -->
    <script src="https://at.captcha.at/sdk.js" defer></script>
    <script>
        window.addEventListener('load', function() {
            // 1. Setup with your public key
            KROT.setup("YOUR-PUBLIC-KEY");
            
            // 2. Set Austrian host (AFTER setup, BEFORE getSolution)
            KROT.KROT_HOST = "https://at.captcha.at";
            
            // 3. Use getSolution() normally
            document.getElementById('myForm').addEventListener('submit', function(e) {
                e.preventDefault();
                
                KROT.getSolution()
                    .then((solution) => {
                        // Send solution to your server for validation
                        fetch('/validate', {
                            method: 'POST',
                            body: JSON.stringify(solution),
                            headers: {'Content-Type': 'application/json'}
                        });
                    });
            });
        });
    </script>
</body>
</html>
```

## Important Notes

- **Performance**: Direct Austria routing may be slower for users outside Europe
- **Reliability**: Uses direct infrastructure without CDN failover
- **Data Processing**: All processing still occurs in Austria regardless of endpoint
- **GDPR Compliance**: Both paths are fully GDPR compliant

## Need More Information?

For questions about network routing or compliance requirements:
- **Email**: infrastructure@captcha.eu
- **Support**: [Contact form](https://www.captcha.eu/contact)
- **Documentation**: [Integration guide](install.md)