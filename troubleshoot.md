# Troubleshooting

This guide helps you resolve common issues with captcha.eu integration.

## Common Issues

### JavaScript SDK Not Loading

**Problem**: The captcha.eu SDK script is not loading on your page.

**Solutions**:
1. Ensure the script tag is placed before your code that uses `KROT`
2. Check browser console for any loading errors
3. Verify your domain is whitelisted in the captcha.eu dashboard
4. Make sure you're using HTTPS for your website

### "KROT is not defined" Error

**Problem**: Getting "KROT is not defined" error when trying to use the SDK.

**Solution**: Make sure the SDK is fully loaded before calling KROT methods:
```javascript
// Wait for SDK to load
window.addEventListener('load', function() {
    KROT.setup("YOUR-PUBLIC-KEY");
});
```

### Validation Always Returns False

**Problem**: Server-side validation always fails even with valid solutions.

**Possible causes**:
1. **Invalid REST key**: Verify you're using the correct REST key for your domain
2. **Solution format**: Ensure you're sending the solution as JSON
3. **Network issues**: Check if your server can reach captcha.eu validation endpoint
4. **Solution expired**: Solutions are valid for a limited time (typically 2 minutes)

### CORS Errors

**Problem**: Getting Cross-Origin Resource Sharing (CORS) errors.

**Solution**: CORS errors typically occur when:
- Testing from `file://` URLs - use a local web server instead
- Domain not properly configured in dashboard
- Using incorrect SDK endpoint

### Form Submission Issues

**Problem**: Form doesn't submit after integration.

**Solutions**:
1. Check console for JavaScript errors
2. Ensure `KROT.interceptForm()` is called after form is loaded
3. Verify the form has proper submit buttons
4. Check if other JavaScript is conflicting

### WordPress Plugin Issues

**Problem**: Plugin not protecting forms.

**Solutions**:
1. Clear WordPress cache
2. Check if checkboxes are enabled in plugin settings
3. Verify API keys are correctly entered
4. Disable conflicting security plugins temporarily

### High False Positive Rate

**Problem**: Legitimate users are being blocked.

**Solutions**:
1. Review your security settings in the dashboard
2. Consider implementing the widget mode for problematic users
3. Check if certain user behaviors trigger false positives
4. Contact support with specific examples

## Getting Help

If you're still experiencing issues:

1. **Check browser console**: Most client-side errors appear here
2. **Enable debug mode**: Add `?debug=true` to your URL for verbose logging
3. **Review integration**: Double-check our [integration guide](install.md)
4. **Contact support**: Reach out via:
   - Live chat on [captcha.eu](https://www.captcha.eu)
   - Email: support@captcha.eu
   - [Contact form](https://www.captcha.eu/contact)

When contacting support, please provide:
- Your domain
- Browser console errors
- Network request/response details
- Steps to reproduce the issue