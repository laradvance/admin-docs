Extension development 
====

> since v1.6.0

`laravel-admin` supports the installation of extension tools to help enrich your background functionality, currently under [https://github.com/laravel-admin-extensions](https://github.com/laravel-admin-extensions) There have been more than a dozen extensions.

If you add some of your own features or components to the `laravel-admin` process, you can make a `laravel-admin` extension, which will help other `laravel-admin` users. And improve the quality of the extension in the feedback of others.

This document will take the development of a `phpinfo` extension as an example, develop an extension step by step, and publish it to others for use. The final effect is referenced [phpinfo](http://demo.laravel-admin.org/phpinfo)

## Create composer package

The `laravel-admin` package will be installed with composer, so first create a composer package. If you have the `v1.6.0` or `dev-master` version installed, you can use the built-in `admin:extend` command. Generate an extended skeleton

> When running the command, you may be prompted to enter a directory to store your extension file. You can add a configuration `'extension_dir' => admin_path('extensions'),` in `config/admin.php`, The extension files will be stored in the `app/Admin/extensions` directory, but you can also put them in any other directory.

```bash
Php artisan admin:extend laravel-admin-ext/phpinfo --namespace=Encore\\PHPInfo
```

Where `laravel-admin-ext/phpinfo` is the package name, and the `namespace` option is the top-level namespace used by this package. After running this command, the directory will be generated in the extension directory `laravel-admin-ext/phpinfo` set in `config/admin.php`.  and the following file structure

```bash
    ├── LICENSE
    ├── README.md
    ├── composer.json
    ├── database
    │   ├── migrations
    │   └── seeds
    ├── resources
    │   ├── assets
    │   └── views
    │       └── index.blade.php
    ├── routes
    │   └── web.php
    └── src
        ├── PHPInfo.php
        ├── PHPInfoServiceProvider.php
        └── Http
            └── Controllers
                └── PHPInfoController.php
```

`resources` is used to place view files and static assets, `src` is mainly used to place logic code, `routes/web.php` is used to store the extension's routing settings, and `database` is used to place database migration files and `seeders`.

## Write code

This extended function is mainly used to integrate the page displayed by the `phpinfo` function in PHP into `laravel-admin`. It will have a route and a view file. There is no database file and static resource file. We can Clean up any files or directories that are not in the directory. The directory files after the cleanup are:
```bash
    ├── LICENSE
    ├── README.md
    ├── composer.json
    ├── resources
    │   └── views
    │       └── index.blade.php
    ├── routes
    │   └── web.php
    └── src
        ├── PHPInfo.php
        ├── PHPInfoServiceProvider.php
        └── Http
            └── Controllers
                └── PHPInfoController.php
```

> After generating the extension skeleton, you may need to debug while developing, so you can refer to [Local installation](#Local Installation) below, install the extension into the laravel app, and continue development.

### Add a route

First add a route, a routing configuration has been automatically generated in `routes/web.php`
```php
<?php
use Encore\PHPInfo\Http\Controllers\PHPInfoController;

Route::get('phpinfo', PHPInfoController::class.'@index');
```

Accessing the path `phpinfo` will handle this request by the `index` method of the `Encore\PHPInfo\Http\Controllers\PHPInfoController` controller.

### Setting extension attributes

`src/PHPInfo.php` as an extension class for setting extended properties

```php
<?php

namespace Encore\PHPInfo;

use Encore\Admin\Extension;

class PHPInfo extends Extension
{
    public $name = 'phpinfo';

    public $views = __DIR__.'/../resources/views';

    public $assets = __DIR__.'/../resources/assets';

    public $menu = [
        'title' => 'Phpinfo',
        'path'  => 'phpinfo',
        'icon'  => 'fa-gears',
    ];
}
```
This file is used to set some properties of this extension. `$name` is the name of this extension. If this extension has a view file that needs to be rendered, you must specify the `$views` attribute of the extension. Also, if there is a static resource file to be published. , you must set the `$assets` property. If you need to add a menu button to the left sidebar, set the `$menu` property.

You can remove unnecessary attributes as needed. After modifying the `src/PHPInfo.php` file, the code is:
```php
<?php

namespace Encore\PHPInfo;

use Encore\Admin\Extension;

class PHPInfo extends Extension
{
    public $name = 'phpinfo';

    public $views = __DIR__ . '/../resources/views';

    public $menu = [
        'title' => 'PHP info',
        'path'  => 'phpinfo',
        'icon'  => 'fa-exclamation',
    ];
}
```

Then open `src/PHPInfoServiceProvider.php`, this `ServiceProvider` will run when `laravel` is started, which is used to register some data of this extension into the application.

### Loading the view

If this extension needs to load the view file, add the following code to the `handle` method of `src/PHPInfoServiceProvider.php`:
```php
if ($views = $extension->views()) {
    $this->loadViewsFrom($views, 'phpinfo');
}
```
The first parameter of the `loadViewsFrom()` method is the view property set in the extension class `src/PHPInfo.php`, and the second parameter is the namespace of the view file directory. After setting it to `phpinfo`, in the controller. Use `view('phpinfo::index')` to load the view file in the `resources/views` directory.

### Introducing static resources

If you have static resource files in your project that need to be imported, first put the files in the `resources/assets` directory, such as `resources/assets/foo.js` and `resources/assets/bar.css`. file

Then set the `$assets` property in the extension class `src/PHPInfo.php`
```php
    public $assets = __DIR__.'/../resources/assets';
```

Then set the release directory in the `handle` method of `src/PHPInfoServiceProvider.php`
```php
if ($this->app->runningInConsole() && $assets = $extension->assets()) {
    $this->publishes(
        [$assets => public_path('vendor/laravel-admin-ext/phpinfo')],
        'phpinfo'
    );
}
```
After the installation is complete, run `php artisan vendor:publish --provider=Encore\PHPInfo\PHPInfoServiceProvider` and the file will be copied to the `public/vendor/laravel-admin-ext/phpinfo` directory.

We need to add these two files into the page when `laravel-admin` is started. You need to add the following code in the `handle` method of `src/PHPInfoServiceProvider.php`.

```php
use use Encore\Admin\Admin;

...

Admin::booting(function () {
    Admin::js('vendor/laravel-admin-ext/phpinfo/foo.js');
    Admin::css('vendor/laravel-admin-ext/phpinfo/bar.css');
});
```

This completes the import of static resources. In the extension of `phpinfo`, since no static resources need to be introduced, this step can be ignored.

### Code Logic Development

The logic of this extension is to extract the PHP configuration data displayed by the `phpinfo` function and then render the new view rendering output. In this step, refer to the core code of [nova-phpinfo](https://github.com/davidpiesse/nova-phpinfo), the modified code reference [PHPInfo.php](https://github.com/laravel -admin-extensions/phpinfo/blob/master/src/PHPInfo.php#L19-L50),

The next step is to get the phpinfo configuration data in the controller by calling the `PHPInfo::toCollection` method, and then render it into the view. In this extension, I omitted the controller step directly in the routing file `routes/web. Render view output in php` configuration
```php
<?php

use Encore\Admin\Layout\Content;
use Encore\PHPInfo\PHPInfo;

$path = PHPInfo::config('path', 'phpinfo');

Route::get($path, function (Content $content, PHPInfo $info) {
    $info = $info->toCollection();
    
    return $content
        ->header('PHP\'s configuration')
        ->description(' ')
        ->body(view('phpinfo::phpinfo', compact('info')));
});
```

Such a complete extension is developed, and the final complete code can be found in [phpinfo](https://github.com/laravel-admin-extensions/phpinfo)

### Modify composer.json & README.md

After the code is partially completed, you need to modify the contents of `composer.json`, replace the contents of `description`, `keywords`, `license`, `authors` with your information, and then don't forget to update `README.md`, supplementary use of documents and other related information.

## Installation

After completing the extended development, you can install your extension in the following way depending on the situation.

### Local installation

In the development process, it is generally necessary to develop while debugging, so first install it locally as follows.

Open the `composer.json` file in your project and add the following configuration
```json
"repositories": [
    {
        "type": "path",
        "url": "app/Admin/extensions/laravel-admin-ext/phpinfo"
    }
]
```

Then run `composer require laravel-admin-ext/phpinfo` to complete the installation. If there are static files to be published, run the following command.
```bash
php artisan vendor:publish --provider=Encore\PHPInfo\PHPInfoServiceProvider
```

This completes the installation, open `http://localhost/admin/phpinfo` to access this extension.

### Remote installation

If the development is complete and you want to open it up for everyone, follow the steps below.

#### Upload to github

Login to your Github first, create a repository, and follow the prompts on the page to push your code up.

```bash
git init
git remote add origin https://github.com/<your-name>/<your-repository>.git
git add .
git commit -am "Initial commit."
git push origin master
```
#### Release release

You can publish the version locally in the following way

```bash
git tag 0.0.1 && git push --tags
```

It can also be manually set on the `Releases` page of Github's repository.

#### Submit to Packagist.org

The next step is to submit your project to `Packagist.org`. If you don't have an account, register one first, then open the [Submit](https://packagist.org/packages/submit) in the top navigation and fill in the repo's github url to submit.

By default, Packagist.org does not automatically update when you push new code, so you need to create a [GitHub Service Hook](https://packagist.org/about#how-to-update-packages). You can also manually update it using the "Update" button on the page, but I recommend automating this process.
 
After the submission is complete, you can install your extension via composer.

#### Join https://github.com/laravel-admin-extensions

If you want to add the extension you developed to [laravel-admin-extensions](https://github.com/laravel-admin-extensions), please contact me in various ways so that more people can see and Use the tools you built.