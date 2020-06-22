# Column basic usage

`model-grid` built-in a lot of the operation of the column, you can use these methods very flexible operation of the column data.

## Column attribute

The column object's `setAttributes()` method is used to add HTML attributes to each row of the current column. A useful scene is to add style to the current column.

```php
$grid->column('title')->setAttributes(['style' => 'color:red;']);
```

The `style()` method is based on the `setAttributes()` method, adding styles directly, such as limiting the width of the column:

```php
$grid->column('desc')->style('max-width:200px;word-break:break-all;');
```

## Fixed column

> Since v1.7.3

If the table has more fields and squeezes the display of the column, you can use the `fixColumns` method to set the fixed column.

```php
$grid->fixColumns(3, -2);
```

The first parameter indicates the first three columns fixed from the beginning, and the second parameter indicates the two columns fixed from the back to the front. (The second parameter can be passed, the default is -1)

## Set column width

```php
$grid->column('title')->width(200);
```

## Set column color

```php
$grid->column('title')->color('#ffff00');
```

## Setting header help information

```php
$grid->column('title')->help('This column is...');
```

A question mark question mark icon will appear on the right side of the header of this column. The mouse hover will pop up the set help information.

## Default hidden column

```php
$grid->column('created_at')->hide();
```

This column will be hidden by default and can be turned on in the column selector in the top right corner.

## Column Sorting

Use the `sortable()` method to set the current column to a sortable sequence

```php
$grid->column('created_at')->sortable();
```

A sort icon will appear in the column header, click to sort

## helpers

### String operations

If the current output data is a string, you can call the method of class `Illuminate\Support\Str`.

For example, the following column shows the string value of the `title` field:

```php
$grid->column('title');
```

Call `Str::limit()` on `title` colum.

Can call `Str::limit()` method on the output string of the `title` column.

```php
$grid->column('title')->limit(30);
```

Continue to call `Illuminate\Support\Str` method:

```php
$grid->column('title')->limit(30)->ucfirst();

$grid->column('title')->limit(30)->ucfirst()->substr(1, 10);

```

### Array operations

If the current output data is a array, you can call the method of class `Illuminate\Support\Collection`.

For example, the `tags` column is an array of data retrieved from a one-to-many relationship:

```php
$grid->column('tags');

array (
  0 =>
  array (
    'id' => '16',
    'name' => 'php',
    'created_at' => '2016-11-13 14:03:03',
    'updated_at' => '2016-12-25 04:29:35',
  ),
  1 =>
  array (
    'id' => '17',
    'name' => 'python',
    'created_at' => '2016-11-13 14:03:09',
    'updated_at' => '2016-12-25 04:30:27',
  ),
)

```

Call the `Collection::pluck()` method to get the `name` column from the array

```php
$grid->column('tags')->pluck('name');

array (
    0 => 'php',
    1 => 'python',
  ),

```

The output data is still a array after above, so you can call methods of `Illuminate\Support\Collection` continue.

```php
$grid->column('tags')->pluck('name')->map('ucwords');

array (
    0 => 'Php',
    1 => 'Python',
  ),
```

Outputs the array as a string

```php
$grid->column('tags')->pluck('name')->map('ucwords')->implode('-');

"Php-Python"
```

### Mixed use

In the above two types of method calls, as long as the output of the previous step is to determine the type of value, you can call the corresponding type of method, it can be very flexible mix.

For example, the `images` field is a JSON-formatted string type that stores a multiple-picture address array:

```php

$grid->column('images');

"['foo.jpg', 'bar.png']"

// chain method calls to display multiple images
$grid->images()->display(function ($images) {

    return json_decode($images, true);

})->map(function ($path) {

    return 'http://localhost/images/'. $path;

})->image();

```

## Extend the column

There are two ways to extend the column function, the first one is through the anonymous function.

Add following code to `app/Admin/bootstrap.php`:

```php
use Encore\Admin\Grid\Column;

Column::extend('color', function ($value, $color) {
    return "<span style='color: $color'>$value</span>";
});
```

Use this extension in `model-grid`:

```php

$grid->column('title')->color('#ccc');

```

If the column display logic is more complex, can implements with a extension class.

Extension class `app/Admin/Extensions/Popover.php`:

```php
<?php

namespace App\Admin\Extensions;

use Encore\Admin\Admin;
use Encore\Admin\Grid\Displayers\AbstractDisplayer;

class Popover extends AbstractDisplayer
{
    public function display($placement = 'left')
    {
        Admin::script("$('[data-toggle=\"popover\"]').popover()");

        return <<<EOT
<button type="button"
    class="btn btn-secondary"
    title="popover"
    data-container="body"
    data-toggle="popover"
    data-placement="$placement"
    data-content="{$this->value}"
    >
  Popover
</button>

EOT;

    }
}
```

And then redister extension in `app/Admin/bootstrap.php`：

```php
use Encore\Admin\Grid\Column;
use App\Admin\Extensions\Popover;

Column::extend('popover', Popover::class);
```

Use the extension in `model-grid`：

```php
$grid->column('desciption')->popover('right');
```
