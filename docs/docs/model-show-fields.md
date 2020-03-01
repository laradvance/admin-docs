# Model-show Fields

## Insert a divider

If you want to add a divider between the fields:
```php
$show->divider();
```

## Modify the display

Modify the display content as follows
```php
$show->title()->as(function ($title) {
    return "<{$title}>";
});

$show->contents()->as(function ($content) {
    return "<pre>{$content}</pre>";
});
```

Here are a few common display style methods implemented by the `as` method:

## image

The content of the field `avatar` is the path or url of the image, which can be displayed as an image:

```php
$show->avatar()->image();
```
The parameters of the `image()` method are referenced to [Field::image()](https://github.com/z-song/laravel-admin/blob/master/src/Show/Field.php#L193)

## file

The content of the field `document` is the path or url of the file, which can be displayed as an file:

```php
$show->avatar()->file();
```
The parameters of the `file()` method are referenced to [Field::file()](https://github.com/z-song/laravel-admin/blob/master/src/Show/Field.php#L216)

## link

The content of the field `homepage` is a url link that can be displayed as an HTML link:
```php
$show->homepage()->link();
```
The parameters of the `link()` method are referenced to [Field::link()](https://github.com/z-song/laravel-admin/blob/master/src/Show/Field.php#L266)

## label

Show the contents of the field `tag` as label:
```php
$show->tag()->label();
```
The parameters of the `label()` method are referenced to [Field::label()](https://github.com/z-song/laravel-admin/blob/master/src/Show/Field.php#L282)

## badge

Show the contents of the field `rate` as badge:
```php
$show->rate()->badge();
```
The parameters of the `badge()` method are referenced to [Field::badge()](https://github.com/z-song/laravel-admin/blob/master/src/Show/Field.php#L302)

## using

If the value of the field `gender` is `f`, `m`, it needs to be displayed with `Female` and `Male` respectively.

```php
$show->gender()->using(['f' => 'Female', 'm' => 'Male']);
```