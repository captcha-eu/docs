# Joomla

We provide a ready to use plugin for contact forms and signups inside your joomla installation.<br />
> The plugin is beta, we are happy to get feedback using one of our contact channels (hello@captcha.eu, website-chat)

In order to enable captcha.eu on your joomla installation follow these steps:


## Download the Extension

Download the latest version of the plugin at the Joomla Extension Directory: <a href='https://extensions.joomla.org/extension/access-a-security/captcha-eu/'>captcha-eu</a>

![Captcha.eu @ JED](files/joomla/captcha-eu-joomla-jed1.png)

## Upload/Enable the extension
Open your joomla admin interface, go to `System->Install->Extensions`

![System-Extension](files/joomla/s1.png)


Upload the previously downloaded `captcha_eu-latest.zip`</a> file and enable the extension.

![Enable-Extension](files/joomla/s2.png)


## Signup at captcha.eu
Go to www.captcha.eu/login and signup, your first **100 validations** are **free**.<br />
After signup go to `domains`  and add a new domain.

When done, you&apos;ll get the Rest-Key and Public Key, head back to your joomla admin interface.

## Add Keys

Go to `System->Plugins->Captcha.eu` copy & paste the keys from captcha.eu dashboard to joomla plugin settings.

In case of a typo or if you entered the wrong key(s), you will be noticed right away on the same page.

![Add Keys](files/joomla/s3.png)


## Enable on Joomla parts

Currently you can enable captcha.eu on:

  - Contact Forms
  - Signup's

You can configure the default captcha under: `System->Global Configuration->Global Configuration->Default Captcha` - this is used for various areas like signup and so on.
![global captcha](files/joomla/s4.png)


To enable it on contact forms, go to `System->Global Configuration->Contacts->Forms`  and select the captcha.eu
![contacts captcha](files/joomla/s5.png)


## RSForms! <sup>**beta**</sup>
> this is a beta plugin, we are happy to receive feedback -> hello@captcha.eu


download the zip ball <a href='files/joomla/rsfpcaptchaeu.zip'>here</a>

Install the zip  in the `System->Extensions->Install`

![rsforms1](files/joomla/rsforms1.png)


Enable the RSForms! Captcha.eu extension

![rsforms2](files/joomla/rsforms2.png)

configure the Extension

![rsforms3](files/joomla/rsforms3.png)

add a field to your RSForm! Form
*caption and description should be empty*

![rsforms2](files/joomla/rsforms4.png)


No go to your frontend and see captcha.eu in action on your form



