# Customize the head navigation bar

Since version `1.5.6`, you can add the html element to the top navigation bar,  open `app/Admin/bootstrap.php`：
```php
use Encore\Admin\Facades\Admin;

Admin::navbar(function (\Encore\Admin\Widgets\Navbar $navbar) {

    $navbar->left('html...');

    $navbar->right('html...');

});
```

method `left` and `right` is used to add the contents of the left and right sides of the head, the method parameters can be any object can be rendered( objects which impletements `Htmlable`、`Renderable`, or has method `__toString()`) or strings.

## Add elements to the left

For example, add a search bar on the left, first create a view `resources/views/search-bar.blade.php`：
```php
<style>

.search-form {
    width: 250px;
    margin: 10px 0 0 20px;
    border-radius: 3px;
    float: left;
}
.search-form input[type="text"] {
    color: #666;
    border: 0;
}

.search-form .btn {
    color: #999;
    background-color: #fff;
    border: 0;
}

</style>

<form action="/admin/posts" method="get" class="search-form" pjax-container>
    <div class="input-group input-group-sm ">
        <input type="text" name="title" class="form-control" placeholder="Search...">
        <span class="input-group-btn">
            <button type="submit" name="search" id="search-btn" class="btn btn-flat"><i class="fa fa-search"></i></button>
          </span>
    </div>
</form>
```
Then add it to the head navigation bar:
```php
$navbar->left(view('search-bar'));
```

## Add elements to the right

```php
$navbar->right(view('search-bar'));
```

## Add links to navigation bar

Open `app/Admin/bootstrap.php` and add the following:

```php
use App\Admin\Extensions\Nav;

Admin::navbar(function (\Encore\Admin\Widgets\Navbar $navbar) {
    $navbar->left(Nav\Link::make('Settings', 'forms/settings'));
    $navbar->right(Nav\Link::make('Register', 'forms/register', 'fa-user'));
});
```

## Add dropdown menu to navigation bar

Open `app/Admin/bootstrap.php` and add the following:

```php
use App\Admin\Extensions\Nav;

Admin::navbar(function (\Encore\Admin\Widgets\Navbar $navbar) {
    $navbar->add(Nav\Shortcut::make([
        'Posts' => 'posts/create',
        'Users' => 'users/create',
        'Images' => 'images/create',
        'Videos' => 'videos/create',
        'Articles' => 'articles/create',
        'Tags' => 'tags/create',
        'Categories' => 'categories/create',
    ], 'fa-plus')->title('Create'));
});
```
