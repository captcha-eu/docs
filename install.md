# Integration
the captcha.eu integration should always consist of two pieces:
  - client side solution generation
  - server side validation of the generated solution

## Client Side


### Javascript


### Setup


```html
<script type="text/javascript" src="https://api.captcha.eu/sdk/sdk.js" defer></script>
```

> at the bottom of your page, or in any sort of document.ready handler.
```js
KROT.setup("xx-MY-PUBLIC-KEY");
```

### Generate Solution Payload
whenever you want to validate that the end user is not a bot, call.

```javascript
KROT.getSolution()
    .then((solution) => {
      // Send this solution to the server.
      // it needs to be JSON.stringify() 
      fetch("https://your-server.com/your/endpoint", {
        method: "POST",
        body: JSON.stringify(solution),
      })
    });
```

for most `<form>` based things there is a pre-built shorthand.

```javascript
var form = document.getElementById("login");
KROT.interceptForm(form);
```

this will  add a hidden field `captcha_at_solution` and intercept the submit buttons.
on submit it sets the buttons to `disabled`  runs the solution generator, adds the response to the `captcha_at_solution`  field
and after successfully solving it will submit the form.


## Server Side

To validate the given solution, you need to make a rest call to the captcha.eu validation endpoint.
below are examples of some programming languages, but a rest-call is enough, feel free to PR a sample in your :heart: language

the data payload needs to be the unmodified version of the client-side solution generation (only `.stringify()`) as the payload is highly dynamic 
and always subject of change.

the response is a JSON:

### Validation Response

```javascript 
{
  "success": true
}
```

### cURL 

```sh
#!/bin/bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H  "Rest-Key: YOUR-REST-KEY" \
  -d '{"challenge": "XXX", "nonce": "XXX", "hash": "XXX"}' \
  https://api.captcha.eu/validate
```


### PHP
> use the http client of your choice, below sample is a raw-curl one, and should not be 1:1 copied to production

```php
<?php
    $solution = $_POST["captcha_at_solution"];

    function checkSolution($solution) {
      $ch = curl_init("https://api.captcha.eu/validate");
      curl_setopt($ch, CURLOPT_POSTFIELDS, $solution);
      curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Rest-Key: YOUR-REST-KEY'));
      curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
      $result = curl_exec($ch);
      curl_close($ch);

      $resultObject = json_decode($result);
      if ($resultObject->success) {
        return true;
      } else {
        return false;
      }
    }

    if(checkSolution($solution)) {
      // It was good
    } else {
      echo "Captcha.eu validation failed";
      exit;
    }
    ?>
```

### OpenAPI (Swagger)

for use with OpenAPI generators please use [this](https://raw.githubusercontent.com/krot-at/docs/master/openapi.yml)
