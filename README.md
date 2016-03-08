#### Forked From https://github.com/hasanilingi/iyzico
This package is fixed version.
## Laravel PHP Framework

[![Build Status](https://travis-ci.org/laravel/framework.svg)](https://travis-ci.org/laravel/framework)
[![Total Downloads](https://poser.pugx.org/laravel/framework/downloads.svg)](https://packagist.org/packages/laravel/framework)
[![Latest Stable Version](https://poser.pugx.org/laravel/framework/v/stable.svg)](https://packagist.org/packages/laravel/framework)
[![Latest Unstable Version](https://poser.pugx.org/laravel/framework/v/unstable.svg)](https://packagist.org/packages/laravel/framework)
[![License](https://poser.pugx.org/laravel/framework/license.svg)](https://packagist.org/packages/laravel/framework)

This package offers simply iyzico laravel bundled payment system API for PHP Framework.

## 1. Installation

The pellempus/iyzico Service Provider can be installed via [Composer](http://getcomposer.org) by requiring the
`pellempus/iyzico` package in your project's `composer.json`. (`composer require pellempus/iyzico dev-master`)

```json
{
    "require": {
        "pellempus/iyzico": "dev-master"
    }
}
```

After need update composer
```
composer update
```

To use the pellempus/iyzico Service Provider, you must register the provider when bootstrapping your Laravel application.

Find the `providers` key in your `config/app.php` and register the pellempus/iyzico Service Provider.

```php
    'providers' => array(
        // ...
        Pellempus\Iyzico\IyzicoServiceProvider::class,
    )
```

Find the `aliases` key in your `config/app.php` and add the AWS facade alias.

```php
    'aliases' => array(
        // ...
        'Iyzico'		  => Pellempus\Iyzico\Facades\Iyzico::class,
    )
```

## 2. Configuration

By default, the package uses the following environment variables to auto-configure the plugin without modification:
```
api_id
secret
```

To customize the configuration file, publish the package configuration using Artisan.

```sh
php artisan vendor:publish
```

Update your settings in the generated `app/config/iyzico.php` configuration file.

```php
return array(

    'api_id' => 'iyzico-api-id',

    'secret' => 'iyzico-secret'

);
```

##3. Bin Check Introduction 

We as iyzico know that BIN information is a valueable part of the checkout page and payment procedure. With this information the user experience on the checkout page can be enchanced greatly. This data requires careful investigation and upto date data which may be burden to handle for our customers. For this we created the BIN List Checker API for our customers.

1)	Request

To get the BIN info you need to make request a POST request to ;
https://api.iyzico.com/bin-check
with your api_id, secret and BIN number.

2)	Response

On the response you will get the following info ;
Card Type (DEBIT CARD / CREDIT CARD),
Issuer (Visa, Master , ...etc)
Brand (Maximum, World, Bonus, ...etc)

###3.1.	Field Explanations

In this part you will find more detailed information about the fields that you are gonna send with the request and the fields you will get on the response.

1) Request Fields

|Field 		| Type/Length 		| Description                             |
|---------|-----------------|-----------------------------------------|
|api_id|AlphaNumeric/32|Api id from iyzico account|
|secret|AlphaNumeric/32|Secret from iyzico account|
|bin|Numeric/6|BIN number you want to get info about|

2) Response Fields

|Field 		| Type/Length 		| Description                              |
|---------|-----------------|------------------------------------------|
|code|Numeric/4|Numeric code representing the status of response (200 for success)|
|status|AlphaNumeric/16|Explanation for status|
|details|object|Object containing the details|
|BIN|Numeric/6|The BIN number from the request|
|CARD_TYPE|AlphaNumeric/20|Card type (DEBIT CARD / CREDIT CARD)|
|ISSUER|AlphaNumeric/10|Issuer institute (Master, Visa, ...)|
|BRAND|AlphaNumeric/10|The card brand (Maximum, World, ...)|
|BANK_CODE|Numeric/3|Bank's code (70,100 , ...)|

###3.2.	Bin Check Error Codes

|Error Code 		| Message														| Reason
|---------------|-----------------------------------|----------------------------------------------------------------------------------------|
|888.777.901 	| Method Not Allowed											| Rather than POST request|
|888.777.902 	| Currently Service Not Available. Try again after some time.	| Service not available|
|888.777.903 	| Bad POST Request.												| api_id, secret, bin parameters not founded in POST request|
|888.777.904 	| Bad POST Requested Params.									| api_id / secret / bin values = null|
|888.777.905 	| Invalid BIN Value.											| BIN value length < 6|
|888.777.906 	| Invalid Merchant.												| api_id & secret not founded/matched|
|888.777.907 	| Unauthorized Merchant											| Merchant Blocked/terminated/rejected|
|888.777.908 	| BIN check is disabled.										| Bin check is disabled for merchant|
|888.777.909 	| Can not find Issuer for given BIN Number						| bin value not matching any issuer|
|000.000.000 	| Successful detailed response									| success|

## 4. Usage

İyzico working principle is two request, two response. We want the first payment forms iyzico like this:

### 4.1 Checkout Usage
```php
  $data = array(
		"customer_language" => "tr",
		"mode" => "test",
		"external_id" => rand(),
		"type" => "CC.DB",
		"installment" => true,
		"amount" => 1099,
		"return_url" => "http://example.com/iyzicoResponse",
		"currency" => "TRY"
	);

	$response = Iyzico::getForm($data);

	echo $response->code_snippet;
```

code_snippet will return to us with means of payment form iyzico.

After payment form approved will send the results to return iyzico mentioned URLs.

```php
  $data = json_decode(Input::get("json"), true);
  var_dump($data);
```
### 4.2 Bin Check Usage
```php
	$response = Iyzico::checkBin($binNumber);
	
	echo $response->code;
```
