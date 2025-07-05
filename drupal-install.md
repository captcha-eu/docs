# Drupal Integration

The captcha.eu Drupal module provides comprehensive bot protection for your Drupal website with seamless integration into the CAPTCHA module ecosystem.

## Installation Guide

### Step 1: Install the Module

Install the captcha.eu module using Composer:

```bash
composer require captcha-eu/drupal
```

Or download manually from [GitHub releases](https://github.com/captcha-eu/drupal/releases) and extract to `/web/modules/custom/captchaeu/`.

### Step 2: Create Your Account
If you haven't already, [sign up for a captcha.eu account](https://www.captcha.eu/login). New accounts include 100 free verification requests for testing.

### Step 3: Set Up Domains
In your captcha.eu dashboard, create a domain entry for your Drupal site. **Important**: Enter your actual website domain (e.g., `example.com`) as this must match where your forms are hosted for security purposes.

### Step 4: Enable Required Modules
Install and enable both the CAPTCHA module and Captcha.EU module:

```bash
drush en captcha captchaeu -y
```

Or enable via the Drupal admin interface at **Extend** → Check both **CAPTCHA** and **Captcha.EU** → **Install**.

### Step 5: Configure API Keys
Navigate to **Configuration** → **People** → **CAPTCHA** → **Captcha.EU** and enter your API keys from the captcha.eu dashboard:
- **Public Key**: Used for client-side integration
- **REST Key**: Used for server-side validation (keep this secret!)

### Step 6: Configure Widget Settings
Choose your preferred protection mode:
- **Invisible Mode**: Seamless protection without visible captcha (recommended)
- **Widget Mode**: Visible "I am human" button with theme options (light, dark, clean, auto)

### Step 7: Enable Protection
Go to **Configuration** → **People** → **CAPTCHA** and add CAPTCHA points for the forms you want to protect:

1. Click **Add CAPTCHA point**
2. Select the form ID from common options:
   - `contact_message_feedback_form` - Contact forms
   - `user_register_form` - User registration
   - `user_login_form` - **Login forms (prevents credential stuffing)**
   - `user_pass` - Password reset forms
   - `comment_comment_form` - Comment forms
3. Choose **Captcha.EU** as the challenge type
4. Save configuration

## Success! Your Drupal Site is Protected

Your Drupal website is now protected against bots. The module supports:

- ✅ **Contact Forms** - Protect inquiry and feedback forms
- ✅ **User Registration** - Prevent fake account creation  
- ✅ **Login Forms** - **Prevent credential stuffing attacks**
- ✅ **Password Reset** - Protect password recovery forms
- ✅ **Comment Forms** - Stop spam comments
- ✅ **Custom Forms** - Works with any Drupal form
- ✅ **Multiple Instances** - Supports multiple captchas per page
- ✅ **Dual Modes** - Invisible protection or visible widget

## Widget Modes

<!-- tabs:start -->

#### **Invisible Mode**

Perfect for seamless user experience. Protection happens automatically without user interaction:

```yaml
Form ID: contact_message_feedback_form
Challenge type: Captcha.EU
Widget Mode: Invisible
```

No visible captcha widget appears, but forms are fully protected.

#### **Widget Mode**

Shows a visible "I am human" button for clear user indication:

```yaml
Form ID: user_login_form  # Ideal for login protection
Challenge type: Captcha.EU  
Widget Mode: Widget
Theme: Auto
```

Users see and click the captcha widget before form submission. **Recommended for login forms** to clearly indicate security protection.

<!-- tabs:end -->

## Credential Stuffing Protection

**Protect your users' accounts** by adding captcha to login forms:

1. Go to **Configuration** → **People** → **CAPTCHA**
2. Click **Add CAPTCHA point**
3. Form ID: `user_login_form`
4. Challenge type: **Captcha.EU**
5. Save configuration

> **Why protect login forms?** Credential stuffing attacks use stolen username/password combinations from data breaches to attempt automated logins. Adding captcha protection prevents these automated attacks while maintaining user experience.

**Recommended settings for login protection:**
- **Widget Mode**: Shows clear security indication to users
- **Theme**: Auto (adapts to your site's design)
- **Invisible Mode**: For seamless protection without user interaction

## Testing Your Integration

To verify everything is working:

1. Visit a protected form on your site
2. Submit the form and check for successful validation
3. Check your captcha.eu dashboard for incoming requests
4. Review our [testing guide](testing.md) for comprehensive validation

> **Preview Button Support**: The module correctly handles both "Preview" and "Send message" buttons, preserving form functionality while adding protection.

## Troubleshooting

### Widget Not Showing
- Verify your Public Key and REST Key are correct
- Check that CAPTCHA point is configured for your form
- Clear Drupal cache: `drush cr`
- Check browser console for JavaScript errors

### Form Submission Issues
- Enable validation logging in Captcha.EU settings
- Check Drupal logs at **Reports** → **Recent log messages**
- Verify your domain matches the captcha.eu dashboard entry
- Ensure your REST Key is correct

### Multiple Captchas Issue
The module automatically generates unique IDs for each form instance, supporting multiple captchas per page without conflicts.

## Development

For local development with DDEV:

```bash
git clone https://github.com/captcha-eu/drupal.git captcha-eu-drupal
cd your-drupal-project
ln -s /path/to/captcha-eu-drupal web/modules/custom/captchaeu
ddev drush en captcha captchaeu -y
```

## Need Help?

- **Module Issues**: Check Drupal logs and enable validation logging
- **API Problems**: Verify your keys in the captcha.eu dashboard  
- **Installation Support**: Review the [GitHub repository](https://github.com/captcha-eu/drupal)
- **General Support**: [Contact our team](https://www.captcha.eu/contact) or use live chat

## Package Information

- **Packagist**: `captcha-eu/drupal`
- **Drupal Version**: 8.9+ / 9.x / 10.x / 11.x
- **License**: GPL-2.0+
- **Dependencies**: `drupal/captcha`