# version update

## View current version

```shell
composer show encore/laravel-admin
```

## Update to the latest version

```shell
composer require encore/laravel-admin -vvv
```

## Update to development version

```shell
composer require encore/laravel-admin:dev-master -vvv
```

## Update the specified version

```shell
composer require encore/laravel-admin: 1.6.15 -vvv
```

## Precautions

Since the front-end resources of each version may be modified, you need to run the following commands after the update.

```shell
/ / Force to publish release assets files
Php artisan vendor:publish --tag=laravel-admin-assets --force

// Force to publish release lang files
php artisan vendor:publish --tag=laravel-admin-lang --force

/ / Clean up the view cache
Php artisan view:clear
```

Finally, don't forget to clean up the browser cache.