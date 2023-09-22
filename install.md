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

```js

import { useState } from "react";
import { useEffect } from "react";

export default function CaptchaValidationPage(props) {
  const [captchaSDKLoaded, setCaptchaSDKLoaded] = useState(false);
  // Load SDK
  useEffect(() => {
    const script = document.createElement("script");
    script.src = "https://www.captcha.eu/sdk.js";
    script.async = true;
    script.onload = () => {
      // ADD YOUR CAPTCHA.EU PUBLIC KEY
      KROT.setup("xxxx");
      setCaptchaSDKLoaded(true);
    };
    document.body.appendChild(script);
  }, []);

  // This generates a solution
  // solution should be added to the form/payload and
  // later on validated on the server
  function getSolution() {
    KROT.getSolution().then((s) => {
       var solution = JSON.stringify(s)
       // send solution to the backend
    });
  }
  if (!captchaSDKLoaded) {
    return <>SDK Loading</>;
  }

  return (
    <>
      <button onClick={getSolution}>Get a solution</button>
    </>
  );
}


```

#### **NextJS**

see a full sample frontend+backend here: https://codesandbox.io/p/sandbox/confident-maria-vgq2gg


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

```js
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
```



#### **Rust**

```rust
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

#### **NextJS**

see a full sample frontend+backend here: https://codesandbox.io/p/sandbox/confident-maria-vgq2gg


#### **Java**

```java
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class CaptchaValidation {

    public static boolean checkSolution(String solution) {
        try {
            URL url = new URL("https://w19.captcha.at/validate");
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();

            // Set the request method to POST
            connection.setRequestMethod("POST");

            // Set request headers
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("Rest-Key", "YOUR-REST-KEY");

            // Enable input/output streams
            connection.setDoOutput(true);

            // Write the solution data to the request
            DataOutputStream outputStream = new DataOutputStream(connection.getOutputStream());
            outputStream.writeBytes(solution);
            outputStream.flush();
            outputStream.close();

            // Get the response code
            int responseCode = connection.getResponseCode();

            if (responseCode == HttpURLConnection.HTTP_OK) {
                // Read the response
                BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
                String inputLine;
                StringBuilder response = new StringBuilder();

                while ((inputLine = in.readLine()) != null) {
                    response.append(inputLine);
                }
                in.close();

                // Parse the JSON response using Gson
                JsonObject jsonObject = JsonParser.parseString(response.toString()).getAsJsonObject();
                if (jsonObject.has("success") && jsonObject.get("success").getAsBoolean()) {
                    return true;
                } else {
                    return false;
                }
            } else {
                // Handle the error case here
                return false;
            }
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
```

#### **Python**

```python
import requests

def check_solution(solution):
    url = "https://w19.captcha.at/validate"
    headers = {
        "Content-Type": "application/json",
        "Rest-Key": "YOUR-REST-KEY"  # Replace with your actual REST API key
    }
    
    try:
        response = requests.post(url, data=solution, headers=headers)
        
        if response.status_code == 200:
            result = response.json()
            if result.get("success"):
                return True
            else:
                return False
        else:
            # Handle the error case here
            return False
    except Exception as e:
        print(e)
        return False

# Unmodified response from the client part
solution = ""  # Replace with your solution data
valid = check_solution(solution)

```

<!-- tabs:end -->


### Validation on the Backend {docsify-ignore}



The response to the validation request is a JSON object with a success property:


```javascript
{
  "success": true
}
```

<!-- tabs:end -->




