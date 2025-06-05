# Frequently Asked Questions

## General Questions

### What is captcha.eu?
captcha.eu is an invisible bot protection service that safeguards your website forms, login screens, and user interactions without requiring users to solve puzzles or click images.

### How does it work?
Our JavaScript SDK analyzes user behavior patterns in real-time to distinguish between humans and bots. This happens invisibly in the background without interrupting the user experience.

### What is a bot? 🤖
A bot is an automated process which submits your forms or executes actions. The goal of the bot might be:
- DDoS attacks ([Wikipedia](https://en.wikipedia.org/wiki/Denial-of-service_attack))
- Enhance chances of winning (Sweepstakes)
- Manipulate data (Polls)
- Search Engines / Crawlers

The range of bot techniques varies a lot - from simple scripts to sophisticated bots that try their best to act like humans. Search Engine Bots are generally considered "good" bots.

### How do you detect bots?
We use multiple detection layers with anonymized data:
- Client-side browser classification
- Behavioral analysis: mouse movement, keyboard timings
- System timing analysis
- Machine learning to generate a likelihood-to-be-human score

### What makes captcha.eu different from other CAPTCHA services?
- **No puzzles**: No image selection or text typing required
- **GDPR compliant**: No cookies or personal data collection
- **EU hosted**: All processing happens in Austrian data centers
- **Accessibility**: Fully accessible for users with disabilities

## Privacy & GDPR

### Is captcha.eu GDPR compliant?
Yes! As a 🇪🇺 EU company, we strictly follow GDPR rules:
- No cookies or localStorage usage
- No cross-page tracking
- Behavioral data is anonymized client-side
- IP addresses are anonymized by removing the last 2 blocks
  - IPv4: `8.8.8.8` → `8.8`
  - IPv6: `2001:db8:3333:4444:5555:6666:7777:8888` → `2001:db8:3333:4444:5555:6666`

### Do I need user consent?
No cookie consent is required since we don't use cookies. However, you should mention captcha.eu in your privacy policy.

### Where is data processed?
All data is processed in our Austrian data centers. No data leaves the EU.

## Pricing & Plans

### Is there a free trial?
Yes! Every new account gets 100 free verification requests to test the service.

### What happens if I exceed my plan limit?
Your protection continues working, but you'll receive notifications to upgrade your plan. We never suddenly stop protection.

### Can I change plans anytime?
Yes, you can upgrade or downgrade your plan at any time. Changes take effect immediately.

### Do you offer custom enterprise plans?
Yes, contact us at enterprise@captcha.eu for custom solutions with higher limits and dedicated support.

## Technical Questions

### Which browsers are supported?
captcha.eu works with all modern browsers:
- Chrome 60+
- Firefox 55+
- Safari 11+
- Edge 79+
- Opera 47+

For detailed requirements, see our [browser requirements](client.md) page.

### Does it work with single-page applications (SPAs)?
Yes! Our SDK is designed to work with React, Vue, Angular, and other SPA frameworks. See framework-specific examples in our [integration guide](install.md).

### Can I use it with multiple domains?
Yes, you can protect multiple domains. Each domain needs to be added in your dashboard and will have its own API keys.

### Does it work with mobile apps?
Currently, captcha.eu is designed for web applications. Mobile app support is on our roadmap.

### What about JavaScript-disabled browsers?
Users with JavaScript disabled will see a message asking them to enable JavaScript for security verification.

## Integration Questions

### How long does integration take?
Basic integration typically takes 10-30 minutes:
- WordPress: 5-10 minutes using our plugin
- Custom websites: 15-30 minutes following our guide

### Do I need backend changes?
Yes, you need to validate the captcha solution on your server. We provide code examples for all major programming languages.

### Can I test locally?
Yes! Add `localhost` as a domain in your dashboard for local development testing.

### Is it compatible with other security plugins?
Generally yes, but some security plugins might need configuration adjustments. See our [troubleshooting guide](troubleshoot.md) for specific plugins.

## Troubleshooting

### The SDK isn't loading
Check that:
1. Your domain is added in the dashboard
2. You're using the correct public key
3. The script tag is properly placed
4. Your site uses HTTPS

### Validation always fails
Common causes:
- Incorrect REST key
- Solution not sent as JSON
- Network connectivity issues
- Expired solutions (valid for 2 minutes)

See our [troubleshooting guide](troubleshoot.md) for detailed solutions.

### Getting "KROT is not defined" error
The SDK hasn't loaded yet. Wrap your code in a load event listener or use the defer attribute on the script tag.

## Machine Learning
For details about our machine learning approach to bot detection, see our [ML documentation](https://www.captcha.eu/technology).

## Support

### How do I get help?
- **Documentation**: You're already here!
- **Live chat**: Available on [captcha.eu](https://www.captcha.eu)
- **Email**: support@captcha.eu
- **Contact form**: [captcha.eu/contact](https://www.captcha.eu/contact)

### What are your support hours?
- Standard support: Monday-Friday, 9 AM - 5 PM CET
- Enterprise support: 24/7 availability

### Do you offer implementation assistance?
Yes, we offer implementation support for enterprise customers. Standard plan users can access our comprehensive documentation and community support.