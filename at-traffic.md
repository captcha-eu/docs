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

To use the Austria-only network path, replace the default endpoint:

### Default Endpoint (CDN-optimized)
```javascript
// Standard integration
KROT.setup("YOUR-PUBLIC-KEY");
```

### Austria-Only Endpoint
```javascript
// Austria-only routing
KROT.setup("YOUR-PUBLIC-KEY", {
  endpoint: "https://at.captcha.at"
});
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