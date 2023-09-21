# Integration

To integrate captcha.eu, there are two components you need to include: client-side solution generation and server-side solution validation. Here's how to set it up:


<!-- tabs:start -->

#### **Client**

### Integration {docsify-ignore}


for the integration in frontend see some of the below samples

<!-- tabs:start -->

#### **Javascript / HTML **

Include the following script at the bottom of your page, or in any document.ready handler:



```html
<script type="text/javascript" src="https://w19.captcha.at/sdk.js" defer></script>
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
    function checkSolution($solution) {
      $ch = curl_init("https://w19.captcha.at/validate");
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

    // Unmodified response from the client part
    $solution = $_POST["captcha_at_solution"];
    $valid = checkSolution($solution);
    // reject request if $valid is not true
?>
```


#### **Go**

```go
type CaptchaResponse struct {
	Success bool `json:"success"`
}

func checkSolution(solution string) (bool, error) {
	url := "https://w19.captcha.at/validate"
	payload := []byte(solution)

	req, err := http.NewRequest("POST", url, bytes.NewBuffer(payload))
	if err != nil {
		return false, err
	}

	req.Header.Set("Content-Type", "application/json")
	req.Header.Set("Rest-Key", "YOUR-REST-KEY") // Replace with your actual REST key

	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		return false, err
	}
	defer resp.Body.Close()

	var result CaptchaResponse
	err = json.NewDecoder(resp.Body).Decode(&result)
	if err != nil {
		return false, err
	}

	return result.Success, nil
}

send the `solution` from the client part into the  `checkSolution()`
depending on the result, reject or accept the request


```

#### **Node**

const http = require('http');
const axios = require('axios');

const YOUR_REST_KEY = 'YOUR-REST-KEY';

function checkSolution(solution) {
  const url = 'https://w19.captcha.at/validate';
  const headers = {
    'Content-Type': 'application/json',
    'Rest-Key': YOUR_REST_KEY,
  };
  const data = { captcha_at_solution: solution };

  return axios.post(url, data, { headers })
    .then(response => response.data.success)
    .catch(error => {
      console.error('Error:', error.message);
      return false;
    });
}




#### **Rust**

```
use serde::Deserialize;
use reqwest::Client;

#[derive(Deserialize)]
struct CaptchaResponse {
    success: bool,
}

async fn check_solution(solution: &str) -> Result<bool, reqwest::Error> {
    let url = "https://w19.captcha.at/validate";
    let client = Client::new();

    let body = serde_json::json!({
        "captcha_at_solution": solution,
    });

    let res = client
        .post(url)
        .header("Content-Type", "application/json")
        .header("Rest-Key", "YOUR REST KEY")
        .json(&body)
        .send()
        .await?;

    let response: CaptchaResponse = res.json().await?;
    Ok(response.success)
}
```

#### **.NET**

```csharp
	public static bool CheckSolution(string solution)
	{
		string url = "https://www.captcha.eu/validate";
		string restKey = "YOUR-REST-KEY";
		HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
		request.Method = "POST";
		request.ContentType = "application/json";
		request.Headers.Add("Rest-Key", restKey);
		using (StreamWriter streamWriter = new StreamWriter(request.GetRequestStream()))
		{
			streamWriter.Write(solution);
			streamWriter.Flush();
			streamWriter.Close();
		}

		using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
		{
			using (StreamReader streamReader = new StreamReader(response.GetResponseStream()))
			{
				string result = streamReader.ReadToEnd();
				Console.WriteLine(result);
				dynamic resultObject = JsonConvert.DeserializeObject(result);
				return resultObject.success;
			}
		}
	}
```

#### **cURL**

```sh
#!/bin/bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H  "Rest-Key: YOUR-REST-KEY" \
  -d '{"challenge": "XXX", "nonce": "XXX", "hash": "XXX"}' \
  https://w19.captcha.at/validate
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




