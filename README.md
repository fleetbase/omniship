# Omniship

**An easy to use, consistent shipping service provider library for PHP**

[![Latest Stable Version](https://poser.pugx.org/omniship/common/version)](https://packagist.org/packages/omniship/common)
[![Total Downloads](https://poser.pugx.org/omniship/common/d/total)](https://packagist.org/packages/omniship/common)

Omniship is a shipping service provider library for PHP. It has been designed based on
ideas from [Omnipay](https://omnipay.thephpleague.com/). It has a clear and consistent API,
is fully unit tested, and even comes with an example application to get you started.

**Why use Omniship instead of a gateway's official PHP package/example code?**

- Because you can learn one API and use it in multiple projects using different shipping service providers
- Because if you need to change shipping service provider you won't need to rewrite your code
- Because most official PHP shipping service provider libraries are a mess
- Because most shipping service providers have exceptionally poor documentation
- Because you are writing a shopping cart and need to support multiple gateways

## TL;DR

Just want to see some code?

```php
use Omniship\Omniship;

$fedex = Omniship::create('FedEx');
$fedex->setApiKey('abc123');

$shimpent = $gateway->createShipment([]);
```

As you can see, Omniship has a consistent, well thought out API. We try to abstract as much
as possible the differences between the various shipping service providers.

## Package Layout

Omniship is a collection of packages which all depend on the
[omniship/common](https://github.com/fleetbase/omniship-common) package to provide
a consistent interface. There are no dependencies on official shipping service provider PHP packages -
we prefer to work with the HTTP API directly. Under the hood, we use the popular and powerful
[PHP-HTTP](http://docs.php-http.org/en/latest/index.html) library to make HTTP requests.
A [Guzzle](http://guzzlephp.org/) adapter is required by default, when using `fleetbase/omniship`.

New service providers can be created by cloning the layout of an existing package. When choosing a
name for your package, please don't use the `omniship` vendor prefix, as this implies that
it is officially supported. You should use your own username as the vendor prefix, and prepend
`omniship-` to the package name to make it clear that your package works with Omnipay.
For example, if your GitHub username was `santa`, and you were implementing the `dhl`
shipping library, a good name for your composer package would be `santa/omniship-dhl`.

## Installation

Omnipay is installed via [Composer](https://getcomposer.org/).
For most uses, you will need to require `fleetbase/omniship` and an individual gateway:

```
composer require fleetbase/omniship fleetbase/omniship-fedex
```

If you want to use your own HTTP Client instead of Guzzle (which is the default for `fleetbase/omniship`),
you can require `fleetbase/omniship-common` and any `php-http/client-implementation` (see [PHP Http](http://docs.php-http.org/en/latest/clients.html))

```
composer require fleetbase/omniship-common fleetbase/omniship-fedex php-http/buzz-adapter
```

## Service Providers

All shipping service providers must implement [ServiceProviderInterface](https://github.com/fleetbase/omniship-common/blob/main/src/Common/ServiceProviderInterface.php), and will usually extend [AbstractServiceProvider](https://github.com/fleetbase/omniship-common/blob/main/src/Common/AbstractServiceProvider.php) for basic functionality.

The following gateways are available:
| Service Provider Package       | Composer Package           | Maintainer                                |
| ------------------------------ | -------------------------- | ----------------------------------------- |
| FedEx                          | `fleetbase/omniship-fedex` | [Fleetbase](https://github.com/fleetbase) |

Service providers are created and initialized like so:

```php
use Omniship\Omniship;

$provider = Omniship::create('FedEx');
$provider->setApiKey('abc_123');
```

Most settings are service provider specific. If you need to query a service provider to get a list
of available settings, you can call `getDefaultParameters()`:

```php
$settings = $provider->getDefaultParameters();
// default settings array format:
array(
    'api_key' => '', // string variable
    'customer_transaction_id' => '', // string variable
);
```