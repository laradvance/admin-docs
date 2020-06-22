# Model-Form

The `Encore\Admin\Form` class is used to generate a data model-based form. For example, there is a`movies` table in the database

```sql
movies
    id          - integer
    title       - string
    director    - integer
    describe    - string
    rate        - tinyint
    released    - enum(0, 1)
    release_at  - timestamp
    created_at  - timestamp
    updated_at  - timestamp

```

The corresponding data model is `App\Models\Movie`, and the following code can generate the`movies` data form:

```php

use App\Models\Movie;
use Encore\Admin\Form;
use Encore\Admin\Facades\Admin;

$form = new Form(new Movie);

// Displays the record id
$form->display('id', 'ID');

// Add an input box of type text
$form->text('title', 'Movie title');

$directors = [
    'John'  => 1,
    'Smith' => 2,
    'Kate'  => 3,
];

$form->select('director', 'Director')->options($directors);

// Add textarea for the describe field
$form->textarea('describe', 'Describe');

// Number input
$form->number('rate', 'Rate');

// Add a switch field
$form->switch('released', 'Released?');

// Add a date and time selection box
$form->dateTime('release_at', 'release time');

// Display two time column
$form->display('created_at', 'Created time');
$form->display('updated_at', 'Updated time');

```

## Custom tools

The top right corner of the form has two button tools by default. You can modify it in the following way:

```php
$form->tools(function (Form\Tools $tools) {

    // Disable `List` btn.
    $tools->disableList();

    // Disable `Delete` btn.
    $tools->disableDelete();

    // Disable `Veiw` btn.
    $tools->disableView();

    // Add a button, the argument can be a string, or an instance of the object that implements the Renderable or Htmlable interface
    $tools->add('<a class="btn btn-sm btn-danger"><i class="fa fa-trash"></i>&nbsp;&nbsp;delete</a>');
});
```

## Form footer

Use the following method to remove the elements of the form foot

```php


$form->footer(function ($footer) {

    // disable reset btn
    $footer->disableReset();

    // disable submit btn
    $footer->disableSubmit();

    // disable `View` checkbox
    $footer->disableViewCheck();

    // disable `Continue editing` checkbox
    $footer->disableEditingCheck();

    // disable `Continue Creating` checkbox
    $footer->disableCreatingCheck();

});

```

## Other methods

Disable submit btn:

```php
$form->disableSubmit();
```

Disable reset btn:

```php
$form->disableReset();
```

Ignore fields to store

```php
$form->ignore('column1', 'column2', 'column3');
```

Set width for label and field

```php
$form->setWidth(10, 2);
```

Set form action

```php
$form->setAction('admin/users');
```

Determine if the current form page is creating or updating

> Since v1.7.6

```php
$form->isCreating();

$form->isUpdating();
```

> Since v1.8.0

Submit confirmation

```php
$form->confirm('确定更新吗？', 'update');

$form->confirm('确定创建吗？', 'create');

$form->confirm('确定提交吗？');
```
