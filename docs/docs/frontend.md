# CSS / JavaScript

## Setting up the website favicon

Add the following code in `app/Admin/bootstrap.php` to set the website's favicon:

```php
Use Encore\Admin\Admin;

Admin::favicon('/your/favicon/path');
```

## Introducing CSS and JavaScript files

If you need to import CSS or JavaScript files, you can add the following code in `app/Admin/bootstrap.php`:

```php
Admin::css('/your/css/path/style.css');
Admin::js('/your/javascript/path/js.js');
```

The path to the file is relative to the `public` directory, and it also supports the introduction of external files:

```php
Admin::js('https://cdn.bootcss.com/vue/2.6.10/vue.min.js');
```

## Insert JS Script Code

If you want to add a JS script code to the current page, you can use `Admin::script()`

```php
Use Encore\Admin\Admin;

Admin::script('console.log("hello world");');
```

This code can be inserted anywhere in the code file where the current request is run, such as some linkage effects between form items, which can be implemented by inserting JS script code.

## Insert CSS Style

If you want to add a CSS code to the current page, you can use `Admin::style()`

```php
Use Encore\Admin\Admin;

Admin::style('.form-control {margin-top: 10px;}');
```

This code can be inserted anywhere in the code file to which the current request is run.

## Insert HTML Code

If you want to add a piece of HTML code to the current page, you can use `Admin::html()`

```php
Use Encore\Admin\Admin;

Admin::html('<template>...</template>');
```

This code can be implemented anywhere by inserting the code file to which the current request is run, such as when adding some hidden HTML code to the current page.

## Minify CSS and JavaScript

> since v1.6.13

This feature is used to compress the local CSS and JS introduced in the background to speed up page access in the background.

This feature relies on [[matthiasmullie/minify]](https://github.com/matthiasmullie/minify) as a compression library, which needs to be installed before use:

```shell
composer require matthiasmullie/minify --dev
```

### Compression

Then run the command `php artisan admin:minify` in the project root directory:

```shell
$ php artisan admin:minify

JS and CSS are successfully minified:
  vendor/laravel-admin/laravel-admin.min.js
  vendor/laravel-admin/laravel-admin.min.css

Manifest successfully generated:
  vendor/laravel-admin/minify-manifest.json
```

This command will generate three files, you can see the effect by looking at the background page source.

### Cleaning up

Run the command `php artisan admin:minify --clear` to clean up the compressed file generated above and return to the state before compression.

```shell
$ php artisan admin:minify --clear

Following files are cleared:
  vendor/laravel-admin/laravel-admin.min.js
  vendor/laravel-admin/laravel-admin.min.css
  vendor/laravel-admin/minify-manifest.json
```

### Configuration

For users who have been updated from a lower version, you need to add a configuration in `config/admin.php`:

```php

'minify_assets' => true,

```