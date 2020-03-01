# Model-show Extension

> since v1.6.12

This feature is used to extend the detail field display. This can be implemented using the built-in display method that does not meet the requirements.

First define the extension class:

```php
<?php

namespace App\Admin\Extensions\Show;

use Encore\Admin\Show\AbstractField;

class UnSerialize extends AbstractField
{
    public function render($arg = '')
    {
        // return any content that can be rendered
        return unserialize($this->value);
    }
}
```

Then register the extension class in `app/Admin/bootstrap.php`
```php
use Encore\Admin\Show;
use App\Admin\Extensions\Show\UnSerialize;

Show::extend('unserialize', UnSerialize::class);
```

Then use this extension in the controller

```php

$show->column()->unserialize('xxx');

```

The arguments passed to the `unserialize()` method are passed to the `UnSerialize::render()` method in order.


You can see several common properties in the parent class `Encore\Admin\Show\AbstractField`.

```php
    /**
     * Field value.
     *
     * @var mixed
     */
    protected $value;

    /**
     * Current field model.
     *
     * @var Model
     */
    protected $model;

    /**
     * If this field show with a border.
     *
     * @var bool
     */
    public $border = true;

    /**
     * If this field show escaped contents.
     *
     * @var bool
     */
    public $escape = true;
```
Where `$value` and `$model` are the current field values ​​and the current data model, which can be used to get the data you want in the `render()` method.

`$border` is used to control whether the current display content requires an outer border, and `$escape` is used to set the current display content to be HTML-escaped.
