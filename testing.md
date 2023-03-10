# Testing
in order to have integration tests sill working while using the captcha.eu protection.
you have multiple options.


## Self-Made
detect the Unit/Integration/Automation-Test yourself, and ignore the response of the validation endpoint (or completly skip the call)


## ByPass Key
there is a special `ByPass` key that you can get inside the dashboard. if you supply the `ByPass` as a solution, in the verification call the call will always succeed

> this still bills your calls
