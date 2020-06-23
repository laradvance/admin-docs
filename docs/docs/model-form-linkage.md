# Form linkage

> Since v1.8.0

Form linkage means that when a specified option of a form item is selected, other form items are displayed in linkage.

![linkage](https://user-images.githubusercontent.com/1479100/82905667-ba6f7980-9f96-11ea-97f1-8f5d565b830c.gif)

The currently supported form linkage components are:

- `select`
- `multipleSelect`
- `belongsTo`
- `belongsToMany`
- `radio`
- `radioButton`
- `radioCard`
- `checkbox`
- `checkboxButton`
- `checkboxCard`

## Instructions

The above components can be divided into two types: single-select and multi-select. Among them, `select`, `radio`, `belongsTo`, `radioButton`, `radioCard` are single-select components, and others are multi-select components.

### Radio components

In the following example, selecting different nationality types will switch to select different linkage form items:

```php
$form->radio('nationality','Nationality')
    ->options([
        1 =>'Home country',
        2 =>'foreign',
    ])->when(1, function (Form $form) {

        $form->text('name','Name');
        $form->text('idcard','ID card');

    })->when(2, function (Form $form) {

        $form->text('name','Name');
        $form->text('passport','Passport');

    });
```

In the above example, the method `when(1, $callback)` is equivalent to `when('=', 1, $callback)`, if you use the operator `=`, you can omit this parameter

At the same time, these operators are also supported, `=`, `>`, `>=`, `<`, `<=`, `!=`, `in`, `notIn`, the usage is as follows:

```php
$form->radio('check')
    ->when('>', 1, function () {

    })->when('>=', 2, function () {

    })->when('in', [5, 6], function () {

    })->when('notIn', [7, 8], function () {

    });
```

How to use `select`, `belongsTo`, `radioButton`, `radioCard` and other components is the same as `radio`.

### Multiple selection components

The multi-select component supports three operators: `=`, `!=`, `has`,

```php
$form->checkbox('nationality','Nationality')
    ->options([
        1 =>'China',
        2 =>'foreign',
    ])->when([1, 2], function (Form $form) {

        $form->text('name','Name');
        $form->text('idcard','ID card');

    })->when('has', 2, function (Form $form) {

        $form->text('name','Name');
        $form->text('passport','Passport');

    });
```

The usage of `multipleSelect`, `belongsToMany`, `checkboxButton`, `checkboxCard` and other components is the same as `checkbox`.
