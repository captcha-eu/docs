# Captcha.eu integration for CraftCMS


## Installation

Install either via plugin store or via composer

```bash
composer require captcha-eu/craftcms
./craft plugin/install captcha-eu
```

## Configuration
You need to add your Public Key (`publicKey`) and Rest Key (`restKey`) from you Captcha.eu account.

go to `/admin/settings/plugins/captcha-eu`  of your installation and configure it.

<img src='files/craft/craft-settings.png'>


> In production environment we strictly recommend to use environment variables to set the  values.


# Template

Make Sure to include

```twig
{{ craft.captchaEU.intercept() }}
```

inside your `<form>` tag.

### Widget mode
By default, captcha.eu runs in **invisible mode** and protects your form in the background. If you would like to show a visible "I am human" widget instead, switch to **widget mode**.

Go to `/admin/settings/plugins/captcha-eu` and set:

- **Widget Mode**: `Invisible` (default) or `Visible widget`
- **Widget Theme**: `Light`, `Dark` or `Auto` (only used in widget mode)


No template changes are required. The same `{{ craft.captchaEU.intercept() }}` renders the widget in widget mode, and the submit button stays disabled until the widget has been solved. Existing installations keep running in invisible mode unless you switch it.


# <img src='https://wp.captcha.eu/wp-content/uploads/2024/03/formie.svg' width=27> Formie

we support formie out of the box, just go to your settings
`/admin/formie/settings/captchas#tab-FormieCaptchaEU`

and enable captcha.eu - also add the key pair.
just like any other captcha used in formie, you can change it's behaviour 
on the "integration" settings of formie.

The same **Widget Mode** and **Widget Theme** options are available in the Formie captcha settings, in the same place where you entered your keys