# Quick start

We use `users` table come with `Laravel` for example,the structure of table is:

```sql
users
    id          - integer
    name        - string
    email       - string
    password    - string
    created_at  - timestamp
    updated_at  - timestamp
```

And the model for this table is `App\User.php`

You can follow these steps to setup `CURD` interfaces of table `users`:

## Add controller

Use the following command to create a controller for `App\User` model

```php
php artisan admin:make UserController --model=App\\User

// under windows use:
php artisan admin:make UserController --model=App\User
```

In `v1.8.0` version, you can use the `admin:controller` command to create a controller:

```php
php artisan admin:controller --model=App\User
```

The above command will create the controller in `app/Admin/Controllers/UserController.php`.

## Add route

Add a route in `app/Admin/routes.php`：

```
$router->resource('demo/users', UserController::class);
```

## Add menu item

Open `http://localhost:8000/admin/auth/menu`, add menu link and refresh the page, then you can find a link item in left menu bar.

> Where `uri` fills in the path part that does not contain the prefix of the route, such as the full path `http://localhost:8000/admin/demo/users`, just input `demo/users`, If you want to add an external link, just fill in the full url, such as `http://laravel-admin.org/`.

## Write CRUD page logic

The controller `app/Admin/Controllers/UserController.php` created by the `admin:make` command is as follows:

```php
<?php

namespace App\Admin\Controllers;

use App\Models\User;
use Encore\Admin\Controllers\AdminController;
use Encore\Admin\Form;
use Encore\Admin\Grid;
use Encore\Admin\Show;

class UserController extends AdminController
{
    protected $title ='Users';

    protected function grid()
    {
        $grid = new Grid(new User());

        $grid->column('id', __('Id'));
        $grid->column('name', __('Name'));
        $grid->column('email', __('Email'));
        $grid->column('password', __('Password'));
        $grid->column('created_at', __('Created at'));
        $grid->column('updated_at', __('Updated at'));

        return $grid;
    }

    protected function detail($id)
    {
        $show = new Show(User::findOrFail($id));

        $show->field('id', __('Id'));
        $show->field('name', __('Name'));
        $show->field('email', __('Email'));
        $show->field('password', __('Password'));
        $show->field('created_at', __('Created at'));
        $show->field('updated_at', __('Updated at'));

        return $show;
    }

    protected function form()
    {
        $form = new Form(new User());

        $form->textarea('name', __('Name'));
        $form->textarea('email', __('Email'));
        $form->textarea('password', __('Password'));

        return $form;
    }
}
```

The `$title` attribute is used to set the title of this CURD module, which can be modified to any other string.

The `grid` method corresponds to the `list` page of the data. Refer to [model-grid documentation](/docs/model-grid.md) to implement the related functional logic of the list page.

The `detail` method corresponds to the `details` page of the data, click on the `detail display` button in the operation column of the list page, and refer to [model-show documentation](/docs/model-show.md) to realize the details page Related functional logic.

The `form` method corresponds to the `create` and `edit` pages of the data. Refer to [model-form documentation](/docs/model-form.md) to implement the relevant functional logic of the data creation and edit pages.
