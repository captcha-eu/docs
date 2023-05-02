# Joomla

we provide a ready to use plugin for contact forms and signups inside your your joomla installation
the plugin is beta, we are happy to get feedback using one of our concate channels (hello@captcha.eu, website-chat)

in order to enable captcha.eu on your joomla installation follow these steps:


## Download the Extension

we are not yet, in the beta, in the JED
download the latest version of the plugin here: <a href='../files/joomla/captcha_eu.zip' download>:floppy_disk: captcha_eu.zip</a> 

## Upload/Enable the extension
open your joomla admin interface, go to `System->extensions`

![System-Extension](files/joomla/s1.png)


Upload the <a href='../files/joomla/captcha_eu.zip' download>:floppy_disk: captcha_eu.zip</a> file.
and enable the extension.

![Enable-Extension](files/joomla/s2.png)


## Signup at captcha.eu
go to www.captcha.eu/login and signup, your first 100 validations are free.
after signup go to `domains`  and add a new domain.

after that you ll get the Rest-Key and Public Key, head back to your joomla admin interface

## Add Keys

go to `System->Plugins->Captcha.eu` copy & paste the keys from captcha.eu dashboard to joomla plugin settings

![Add Keys](files/joomla/s3.png)


## Enable Captcha.eu on Joomla parts

Currently you can enable captcha.eu on:

  - Contact Forms
  - Signup's

you can configure the default captcha under: `System->Global Configuration->Global Configuration->Default Captcha` - this is used for various areas like signup and so on.
![global captcha](files/joomla/s4.png)


to enable it on contact forms, go to `System->Global Configuration->Contacts->Forms`  and select the captcha.eu 
![contacts captcha](files/joomla/s5.png)
