# Integration

To integrate captcha.eu, there are two components you need to include: client-side solution generation and server-side solution validation. Here's how to set it up:


<!-- tabs:start -->

#### **Client Side**

### Integration {docsify-ignore}


for the integration in frontend see some of the below samples

<!-- tabs:start -->

#### **Javascript / HTML **

Include the following script at the bottom of your page, or in any document.ready handler:



```html
<script type="text/javascript" src="https://www.captcha.eu/sdk.js" defer></script>
```

Then, set up the Captcha-eu client-side SDK with your public key:



```javascript
KROT.setup("xx-MY-PUBLIC-KEY");
```


To generate a solution payload for server-side validation, call `KROT.getSolution()`:




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

If you're using a form, you can use the pre-built shorthand:



```javascript
var form = document.getElementById("login");
KROT.interceptForm(form);
```

This adds a hidden field `captcha_at_solution` and intercepts the submit buttons. On submit, it sets the buttons to disabled, runs the solution generator, adds the response to the `captcha_at_solution` field, and after successful solving, submits the form.


#### **React**

REACT Sample

<!-- tabs:end -->




#### **Backend **

To validate the given solution, make a REST call to the captcha.eu validation endpoint. Here are examples for several programming languages:

<!-- tabs:start -->

#### ** PHP **

Use the HTTP client of your choice. Here is an example using raw cURL, which should not be copied to production:

```php
<?php
    $solution = $_POST["captcha_at_solution"];

    function checkSolution($solution) {
      $ch = curl_init("https://www.captcha.eu/validate");
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


#### **Go**

Go

#### **Node**

Node

#### **Rust**

Node

#### **.NET**

Node

#### **cURL**

```sh
#!/bin/bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H  "Rest-Key: YOUR-REST-KEY" \
  -d '{"challenge": "XXX", "nonce": "XXX", "hash": "XXX"}' \
  https://www.captcha.eu/validate
```

#### **OpenAPI/Swagger**

For use with OpenAPI generators please use [this](https://raw.githubusercontent.com/captcha-eu/docs/master/openapi.yml).



<!-- tabs:end -->


### Validation on the Backend {docsify-ignore}



The response to the validation request is a JSON object with a success property:


```javascript
{
  "success": true
}
```

<!-- tabs:end -->




