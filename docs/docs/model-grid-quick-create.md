# Quick create

> Since v1.7.3

After enable this feature in the grid, a form will be added to the table header to create the data. For some simple form pages, it is convenient to quickly create data without jumping to the create page.

<img width="663" alt="WX20190722-004700@2x" src="https://user-images.githubusercontent.com/1479100/61594099-4b105700-ac1a-11e9-864a-6c5ee2312b78.png">

To enable quick create:

```php
$grid->quickCreate(function (Grid\Tools\QuickCreate $create) {
    $create->text('name', 'Name');
    $create->email('email', 'Email');
});
```

> Note：Each field in the form, and the same type of form field should be set on the create form page.

The form fields supported by the form have the following types

## Text input

```php
$create->text('column_name', 'placeholder...');
```

## Email input box

```php
$create->email('column_name', 'placeholder...');
```

## IP input box

```php
$create->ip('column_name', 'placeholder...');
```

## URL input box

```php
$create->url('column_name', 'placeholder...');
```

## Password input box

```php
$create->password('column_name', 'placeholder...');
```

## Mobile number input

```php
$create->mobile('column_name', 'placeholder...');
```

## Integer input box

```php
$create->integer('column_name', 'placeholder...');
```

## Select

```php
$create->select('column_name', 'placeholder...')->options([
    1 => 'foo',
    2 => 'bar',
]);
```

## Multiple select

```php
$create->multipleSelect('column_name', 'placeholder...')->options([
    1 => 'foo',
    2 => 'bar',
]);
```

## Date time selection

Time and date input box

```php
$create->datetime('column_name', 'placeholder...');
```

## Time selection

Time input box

```php
$create->time('column_name', 'placeholder...');
```

## Date selection

```php
$create->date('column_name', 'placeholder...');
```