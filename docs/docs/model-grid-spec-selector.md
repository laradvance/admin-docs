# Specification selector

> Since v1.7.6

This feature is used to build specifications like:

![WX20190829-114416](https://user-images.githubusercontent.com/1479100/63954478-be2ca900-cab5-11e9-8ea2-e5ee9b8726ae.png)

## Basic usage

As shown in the following code, assuming that the four values ​​of the `brand` field correspond to four brands, the following way will build a specification selector for `brand`.

```php
$grid->selector(function (Grid\Tools\Selector $selector) {
    $selector->select('brand', 'Brand', [
        1 => 'Huawei',
        2 => 'MI',
        3 => 'OPPO',
        4 => 'vivo',
    ]);
});
```

The `select` method is multi-select by default. Click the 'plus' on the right side of each option on the page. The query for this field will add a query option. If the field filter only allows one to be selected, use the `selectOne` method.

```php
$selector->selectOne('brand', 'Brand', [
    1 => 'Huawei',
    2 => 'MI',
    3 => 'OPPO',
    4 => 'vivo',
]);
```

## Custom Query
   
The above method will use the value selected on the selector to query as a query condition, but in some cases you need more flexible control query mode, you can customize the query in the following way:

```php
$selector->select('price', 'price', ['0-999', '1000-1999', '2000-2999'], function ($query, $value) {
    $between = [
        [0, 999],
        [1000, 1999],
        [2000, 2999],
    ];

    $query->whereBetween('price', $between[$value]);
});
```

As shown above, passing an anonymous function as the fourth parameter, after the price field `price` is selected, the logic in the anonymous function will be used for data query, so that you can define any query method.

> The second parameter of the `select` and `selectOne` methods is the selector label, which can be omitted. If omitted, the field name will be used automatically.