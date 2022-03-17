---
title: "Laravel 8 Add Custom Class Facades"
description: "Facades over Type-Hints!"
images:
  - facade.jpg
date: 2022-03-17T09:23:23+08:00
draft: false
toc: true
images:
tags:
  - coding
  - tutorial
---
So, you are having a good time crafting your application using Laravel.

Not just that, you keep using the facade feature of existing classes.

As your application grows, somewhere in the timeline you will need to integrate custom services.

_However_...

The facade feature has been convenient to you, so how do we turn our custom classes into facade?

## Laravel, to the rescue! (_again_)
Laravel is an _extensible_ framework allowing you to plug in and out, disable here and there, configure now and then. Basically, you're free to do as you wish.

[Facade](https://laravel.com/docs/9.x/facades) included.

## Steps
We'll make a custom `Payment` service and have facade available to the class.
### 1. Create Custom Class
Personally, I'll create another folder `Services` in `App` directory.
```php
// App/Services/Payment.php

namespace App\Services;

class Payment 
{
  public function test(): string
  {
    return 'Test';
  }
}
```
### 2. Create Custom Provider
Next, integrating the custom service will need Laravel to recognize the service. Always register your service as provider to keep the pattern clean.

`php artisan make:provider ServiceProviders/PaymentProvider`

```php
namespace App\Providers\ServiceProviders;

use App\Services\Payment;
use Illuminate\Support\ServiceProvider;

class PaymentProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->bind('payment', function () {
            return new Payment;
        });
    }
}

```
### 3. Create Custom Facade
As there is no command to quickly create a facade class, we'll need to manually create the file.
Below, I have created a `PaymentFacade.php` file in the `app/Services/Facades` directory. Create the directory if you have not done so.
```php
namespace App\Services\Facades;

use Illuminate\Support\Facades\Facade;

class PaymentFacade extends Facade 
{
    protected static function getFacadeAccessor()
    {
        return 'payment';
    }
}
```
### 4. Register Provider and Facade
We'll have to register both provider and facade so Laravel can bind them to the application.

Edit your `config/app.php` to add your newly create provider and facade classes.

```php
// config/app.php

'providers' => [
  ...
  App\Providers\EventServiceProvider::class,
  App\Providers\RouteServiceProvider::class,
  App\Providers\Services\PaymentProvider::class,
]

'aliases' => [
  ...
  'Validator' => Illuminate\Support\Facades\Validator::class,
  'View' => Illuminate\Support\Facades\View::class,
  'Payment' => App\Services\Facades\PaymentFacade::class,
]
```
### 5. Import the Facade
Well done!

Your custom service class is now accessible as facade!

```php
// web.php

use Facades\App\Services\Payment;

Route::get('/check', function() {
  dd(Payment::test());
});

```
### Result
{{< image src="/facade.png" >}}