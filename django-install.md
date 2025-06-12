# <img src="https://www.django-rest-framework.org/img/logo.png" alt="Django Logo" width="50" height="50" style="vertical-align: middle;"> Django Integration

Protect your Django applications with captcha.eu's invisible CAPTCHA. This guide shows you how to integrate captcha.eu into your Django project using our Python SDK.

## Overview

captcha.eu provides invisible CAPTCHA protection for your Django forms without requiring users to solve puzzles or click checkboxes. The integration analyzes user behavior in real-time to detect bots while remaining completely invisible to legitimate users.

**Features:**
- 🔒 Invisible protection - no user interaction required
- 🇪🇺 GDPR compliant - EU-hosted with no tracking
- ♿ Accessible for all users
- 🚀 Real-time behavioral analysis
- 📱 Works on all devices and browsers

## Step 1: Create Your captcha.eu Account

Before installing the Django integration, you'll need a captcha.eu account:

1. **Sign up** at [captcha.eu](https://captcha.eu)
2. **Add your domain** in the dashboard
3. **Get your API keys** (Public Key and REST Key)
4. **Start with 100 free validations** - no credit card required

## Step 2: Clone the Sample Project

We've created a complete Django sample application that you can use as a starting point:

```bash
git clone https://github.com/captcha-eu/django-sample.git
cd django-sample
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

The sample includes:
- Django 4.2+ with basic project structure
- Contact form with captcha protection
- Bootstrap UI for professional appearance
- Complete frontend and backend integration

## Step 3: Configure Your API Keys

Edit `captcha_demo/settings.py` and add your captcha.eu credentials:

```python
# Captcha.eu Configuration
CAPTCHA_EU_PUBLIC_KEY = 'your-public-key-here'
CAPTCHA_EU_REST_KEY = 'your-rest-key-here'
CAPTCHA_EU_BYPASS_KEY = 'your-bypass-key-here'  # For testing only
```

⚠️ **Security Note**: Never commit your REST key to version control. Consider using environment variables for production:

```python
import os
CAPTCHA_EU_PUBLIC_KEY = os.getenv('CAPTCHA_EU_PUBLIC_KEY')
CAPTCHA_EU_REST_KEY = os.getenv('CAPTCHA_EU_REST_KEY')
```

## Step 4: Run the Sample Application

```bash
# Run database migrations
python manage.py migrate

# Start the development server
python manage.py runserver
```

Visit `http://127.0.0.1:8000` to see the sample application with captcha protection.

## Step 5: Test the Integration

The sample application includes two ways to test the captcha:

1. **Protected Form**: Fill out the contact form normally - it's protected by invisible captcha
2. **Test Button**: Use the "Send without captcha" button to see validation failure

### Success Indicators:
- ✅ Form submissions work normally for legitimate users
- ✅ Bot submissions are blocked with "Captcha validation failed" message
- ✅ No user interaction required (completely invisible)

## Integration Details

### Frontend Integration

The sample shows how to integrate the captcha.eu JavaScript SDK:

```html
<!-- Load the captcha.eu SDK -->
<script type="text/javascript" src="https://www.captcha.eu/sdk.js" defer></script>

<script>
// Initialize captcha with your public key
KROT.setup("{{ captcha_public_key }}");

// Handle form submission
form.addEventListener('submit', function(e) {
    e.preventDefault();
    
    // Get captcha solution
    KROT.getSolution()
        .then((solution) => {
            // Add solution to form and submit
            document.getElementById('captcha_solution').value = JSON.stringify(solution);
            form.submit();
        });
});
</script>
```

### Backend Validation

Server-side validation in Django views:

```python
import json
import requests
from django.conf import settings

def validate_captcha(solution):
    """Validate captcha solution with captcha.eu API"""
    url = "https://www.captcha.eu/validate"
    headers = {
        "Content-Type": "application/json",
        "Rest-Key": settings.CAPTCHA_EU_REST_KEY
    }
    
    try:
        if isinstance(solution, str):
            solution_data = json.loads(solution)
        else:
            solution_data = solution
            
        response = requests.post(url, json=solution_data, headers=headers)
        
        if response.status_code == 200:
            result = response.json()
            return result.get("success", False)
        else:
            return False
    except Exception as e:
        print(f"Captcha validation error: {e}")
        return False

# In your view
def contact_view(request):
    if request.method == 'POST':
        captcha_solution = request.POST.get('captcha_solution', '')
        
        if validate_captcha(captcha_solution):
            # Process form - user is legitimate
            return redirect('success')
        else:
            # Block submission - likely a bot
            messages.error(request, 'Captcha validation failed.')
    
    return render(request, 'contact.html')
```

## Integration into Existing Projects

To add captcha.eu to your existing Django project:

### 1. Install Dependencies

Add to your `requirements.txt`:
```
requests>=2.28.0
```

### 2. Update Settings

Add captcha configuration to your Django settings:

```python
# Captcha.eu Configuration
CAPTCHA_EU_PUBLIC_KEY = 'your-public-key-here'
CAPTCHA_EU_REST_KEY = 'your-rest-key-here'
```

### 3. Create Validation Function

Copy the `validate_captcha` function from the sample into your views or create a utility module.

### 4. Update Templates

Add the captcha.eu SDK to your base template:

```html
<script type="text/javascript" src="https://www.captcha.eu/sdk.js" defer></script>
```

### 5. Protect Forms

For each form you want to protect:

1. Add hidden field: `<input type="hidden" name="captcha_solution" id="captcha_solution">`
2. Add JavaScript to get solution before submission
3. Add server-side validation in your view

## Django Form Integration

For Django Forms integration, you can create a custom form field:

```python
from django import forms

class CaptchaField(forms.CharField):
    def __init__(self, *args, **kwargs):
        kwargs['widget'] = forms.HiddenInput()
        kwargs['required'] = False
        super().__init__(*args, **kwargs)

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
    captcha_solution = CaptchaField()

    def clean_captcha_solution(self):
        solution = self.cleaned_data.get('captcha_solution')
        if not validate_captcha(solution):
            raise forms.ValidationError('Captcha validation failed.')
        return solution
```

## Common Use Cases

### Contact Forms
Perfect for contact forms, newsletter signups, and feedback forms.

### User Registration
Protect user registration forms from automated account creation.

### Comment Systems
Prevent spam comments while maintaining user experience.

### E-commerce Forms
Secure checkout forms and product inquiries.

### API Endpoints
Validate captcha solutions in Django REST Framework APIs.

## Testing and Development

### Bypass Key for Testing
Use your bypass key during development to skip captcha validation:

```python
def validate_captcha(solution):
    # Skip validation in development with bypass key
    if settings.DEBUG and solution == settings.CAPTCHA_EU_BYPASS_KEY:
        return True
    # Normal validation...
```

### Unit Testing
Mock the captcha validation in your tests:

```python
from unittest.mock import patch

class ContactFormTest(TestCase):
    @patch('myapp.views.validate_captcha')
    def test_contact_form_success(self, mock_validate):
        mock_validate.return_value = True
        # Test form submission...
```

## Production Deployment

### Environment Variables
Use environment variables for production:

```bash
export CAPTCHA_EU_PUBLIC_KEY="your-public-key"
export CAPTCHA_EU_REST_KEY="your-rest-key"
```

### Error Handling
Implement proper error handling for production:

```python
def validate_captcha(solution):
    try:
        # Validation logic...
    except requests.exceptions.RequestException:
        # Log error and fail secure
        logger.error("Captcha service unavailable")
        return False
    except Exception as e:
        logger.error(f"Captcha validation error: {e}")
        return False
```

### Performance Optimization
- Cache successful validations temporarily
- Use async requests for non-blocking validation
- Implement fallback behavior for service unavailability

## Troubleshooting

### Common Issues

**Captcha validation always fails:**
- Check that your REST key is correct
- Verify domain matches your captcha.eu dashboard settings
- Ensure JSON payload is properly formatted

**JavaScript errors:**
- Verify the SDK is loaded before calling KROT.setup()
- Check browser console for any errors
- Ensure public key is correctly passed to template

**Form not submitting:**
- Check that preventDefault() is called properly
- Verify captcha solution is added to form data
- Test with browser developer tools

### Debug Mode
Enable debug logging to troubleshoot issues:

```python
import logging
logging.basicConfig(level=logging.DEBUG)

def validate_captcha(solution):
    # Add debug logging...
    logger.debug(f"Validating solution: {solution[:50]}...")
```

## Support

- **Sample Code**: [GitHub Repository](https://github.com/captcha-eu/django-sample)
- **Documentation**: [captcha.eu docs](https://captcha.eu/docs)
- **Dashboard**: [captcha.eu dashboard](https://captcha.eu/dashboard)
- **Contact**: Available through your captcha.eu dashboard

## Security Best Practices

1. **Never expose REST keys** in client-side code
2. **Use HTTPS** in production
3. **Validate on server-side** - never trust client-only validation
4. **Implement rate limiting** for additional protection
5. **Monitor validation rates** in your dashboard
6. **Use environment variables** for sensitive configuration

---

**Ready to get started?** Clone the [sample project](https://github.com/captcha-eu/django-sample) and have captcha protection running in minutes!