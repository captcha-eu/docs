# PHP

## Install

```
composer require captcha-eu/sdk
```


## Validate

```php
use CaptchaEU\Service as CaptchaService;

$validator = new CaptchaService("YOUR-PUBLIC-KEY", "YOUR-PRIVATE-KEY", "https://www.captcha.eu");
$valid = $validator->validate($_POST["captcha_at_solution"]);
if($valid) {
    // Proceed with your business logic
} else {
    // Fail the request, since captcha is invalid    
}
```

## Frontend 

there a are a few frontend helpers included.

### SDK 
this will output the required script tags.
```php 
echo $validator->script();
```

### Protect Form

To Attach to an existing form use.

```php
echo $validator->protectForm("my-form-id");
```


# <img src="https://wp.captcha.eu/wp-content/uploads/2023/10/1969px-Laravel.svg.png" height=27> Laravel
this package can also be directly integrated into any laravel application.


add the following to your `config/app.php`

```php
'providers' => [
    ...

    /*
     * Package Service Providers...
     */
    CaptchaEU\Laravel\ServiceProvider::class,

    ...
],

'aliases' => [
    ...
    "CaptchaEU" => CaptchaEU\Laravel\Facades\CaptchaEU::class
    ...
],
```

*Publish Config*

`php artisan vendor:publish --provider="CaptchaEU\Laravel\ServiceProvider"`


## Configration

use the following envs.:

```sh
CAPTCHA_EU_PUBLICKEY="Public Key"
CAPTCHA_EU_PRIVATEKEY="Private Key"
CAPTCHA_EU_ENDPOINT="https://www.captcha.eu"
```


## Load the script


```html
{!! CaptchaEU::script() !!}
```


## Protect Form

```html
 {!! CaptchaEU::protectForm("myform") !!}
```

## Validate on POST

```php
$validator = Validator::make($request->all(), [
  'captcha_at_solution' => 'captcha_eu',
]);
$failed = $validator->fails();
```


## Sampler
Here is a fully Running laravel app: https://github.com/captcha-eu/laravel-sampler


```sh
git clone https://github.com/captcha-eu/laravel-sampler
cd laravel-sampler
composer install
php artisan serve
```


navigato to your host http://localhost:8000/contact
