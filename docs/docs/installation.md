## Installation

> This package requires PHP 7+ and Laravel 5.5+

```
composer create-project muhlisabdi/crud-admin blog
```

Update .env file based on your configuration

```
php artisan admin:install
```

Open `http://localhost/admin/` in browser,use username `admin` and password `admin` to login.

## Generated files

After the installation is complete, the following files are generated in the project directory:

### Configuration file

After the installation is complete,all configurations are in the `config/admin.php` file.

### Admin files
After install,you can find directory`app/Admin`,and then most of our develop work is under this directory.

```
app/Admin
├── Controllers
│   ├── ExampleController.php
│   └── HomeController.php
├── bootstrap.php
└── routes.php
```

`app/Admin/routes.php` is used to define routes.

`app/Admin/bootstrap.php` is bootstrapper for laravel-admin, more usages see comments inside it.

The `app/Admin/Controllers` directory is used to store all the controllers, The `HomeController.php` file under this directory is used to handle home request of admin,The `ExampleController.php` file is a controller example.

### Static assets

The front-end static files are in the `/public/packages/admin` directory.