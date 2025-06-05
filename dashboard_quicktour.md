# :rocket: Quickstart Guide

Get your website protected against bots in just 5 simple steps. This guide will walk you through the entire process from signup to implementation.

## 1. **Create Your Account**

Visit [captcha.eu](https://www.captcha.eu/) and click the signup button. After registration, you'll get:
- Access to your personal dashboard
- 100 free verification requests to test
- Ability to manage multiple domains

## 2. **Choose Your Plan**

Select a subscription that fits your traffic:
- **Starter**: Perfect for small websites (up to 1,000 requests/month)
- **Growth**: For growing businesses (up to 10,000 requests/month)
- **Advanced**: High-traffic sites (up to 100,000 requests/month)
- **Enterprise**: Custom solutions for large organizations

💡 *Tip: You can change plans anytime without losing service.*

## 3. **Add Your Domain(s)**

In your dashboard:
1. Click "Add Domain"
2. Enter a name (can be any identifier, not just the URL)
3. Each domain gets unique API keys

⚠️ **Important**: Keep your REST key secret - it's used for server-side validation.

## 4. **Integrate the Code**

### Quick Integration
For the fastest setup:
- **WordPress?** Use our [plugin](wordpress-install.md)
- **Other CMS?** Check our [platform guides](_sidebar.md#platforms)
- **Custom site?** See our [integration guide](install.md)

### Basic Example
```html
<script src="https://www.captcha.eu/sdk.js" defer></script>
<script>
  KROT.setup("YOUR-PUBLIC-KEY");
</script>
```

## 5. **Verify Protection**

Once integrated:
1. [Test your forms](testing.md) to ensure protection is active
2. Monitor statistics in your dashboard
3. Check our [REST API](rest.md) for detailed analytics

✅ **You're protected!** Your forms are now secure from bots.

## Next Steps

- 📊 [Configure advanced settings](https://www.captcha.eu/login) in your dashboard
- 🧪 Learn about [testing strategies](testing.md)
- 🔧 Explore [platform-specific features](install.md)
- 🌍 Use our [Austria-only endpoint](at-traffic.md) for 100% EU traffic

## Need Help?

We're here to assist:
- 💬 **Live Chat**: Available on [our website](https://www.captcha.eu)
- 📧 **Email**: support@captcha.eu
- 📚 **Docs**: You're already here!
- 🐛 **Issues**: [Contact form](https://www.captcha.eu/contact)

