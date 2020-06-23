# JSON format field processing

Model form provides the following components to process fields in `JSON` format, which is convenient for processing objects in the format of `JOSN`, one-dimensional arrays, two-dimensional arrays and other objects.

Before using the following components, **must** first set the cast of this field in the model

```php
class Foo extends Model
{
    protected $casts = [
        'column_name' =>'json',
    ];
}
```

## Key-value objects

![QQ20200519-125825](https://user-images.githubusercontent.com/1479100/82286657-c70d3400-99d0-11ea-9ff0-c29ba06bcded.png)

If your field stores the `{"field":"value"}` format , you can use the `keyValue` component:

```php
$form->keyValue('column_name');

// Set verification rules
$form->keyValue('column_name')->rules('required|min:5');
```

## Fixed key-value object

![QQ20200519-130014](https://user-images.githubusercontent.com/1479100/82286671-cf656f00-99d0-11ea-8b48-6c49fe74731e.png)

Used to process `JSON` type field data of `mysql` or `object` type data of `mongodb`, and also store data values ​​of multiple fields in the form of `JSON` string in `mysql` string type In the field

Applicable to JSON type fields with fixed key values

```php
$form->embeds('column_name', function ($form) {

    $form->text('key1')->rules('required');
    $form->email('key2')->rules('required');
    $form->datetime('key3');

    $form->dateRange('key4','key5','Range')->rules('required');
});

// Custom title
$form->embeds('column_name','Field title', function ($form) {
    ...
});
```

The method call inside the callback function to construct the form element is the same as outside.

## One-dimensional array

![QQ20200519-125926](https://user-images.githubusercontent.com/1479100/82286670-ce344200-99d0-11ea-8003-6481fa92410e.png)

If your field is used to store a one-dimensional array in the format [[foo], "Bar"], you can use the list component:

```php
$form->list('column_name');

// Set verification rules
$form->list('column_name')->rules('required|min:5');

// Set the maximum and minimum number of elements
$form->list('column_name')->max(10)->min(5);

```

## Two-dimensional array

> since v.16.13

![WX20190505-124413](https://user-images.githubusercontent.com/1479100/57188574-8a8ca880-6f33-11e9-8e64-6dc44976cf68.png)

If a field stores a two-dimensional array in `json` format, you can use the `table` form field:

```php

$form->table('column_name', function ($table) {
    $table->text('key');
    $table->text('value');
    $table->text('desc');
});
```

At the same time, you need to set the accessor and modifier for this field in the model:

```php

    public function getColumnNameAttribute($value)
    {
        return array_values(json_decode($value, true) ?: []);
    }

    public function setColumnNameAttribute($value)
    {
        $this->attributes['column_name'] = json_encode(array_values($value));
    }
```

This component is similar to the `hasMany` component, but it is used to handle the case of a single field and is suitable for simple two-dimensional data.
