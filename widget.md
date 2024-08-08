# Widget

for integration of a more visible way, to show the user that this page/form is protected with a captcha you might
want to use the widget.

# Integration

You need to add to your head

```html
<script type="text/javascript" src="https://www.captcha.eu/sdk.js" defer></script>
```

and then add the widget within your `<form></form>`


```html
<div id="captcha_eu_id" class="cpt_widget" data-key="YOUR_PUBLIC_KEY">
```


initialize all widgets:

```js
<script>
KROT.init();
</script>
```


To validate - please see [Integration](/install) on how to validate the token on your backend/application, by default the payload is in the form-field named `captcha_at_hidden_field`


|                                                 |                                  |  |
| -------------------------------------------------------- | -------------------------------------- | ------- |
| <img src="files/widget/widget1.png">                                   | <img src="files/widget/widget2.png"> |  <img src="files/widget/widget3.png">     |



## Additional Widget options

| Attribute                                                | Values                                 | Description |
| -------------------------------------------------------- | -------------------------------------- | ------- |
| `data-field-selector`                                    | a selector for the target hidden field |  the final validation payload will be written into this dom element (usually a `<input type='hidden'>`), by default captcha creates a new one named: `captcha_at_hidden_field`     |
| `data-language-force`                                    | force specific laguage                 |  widget detecs language via browser, if you'd like to force one set it, possible values: `en`, `de`, `fr`, `ru`, `uk`, `zh`, `hr`, `sr`, `no`, `sv`, `lt`, `da`, `nl`, `it`, `es`    |
| `data-theme         `                                    | force theme                            |  by default the widget detect's OS based light/dark mode, you can force it, possible values: `dark`, `light`, `clean`   |




