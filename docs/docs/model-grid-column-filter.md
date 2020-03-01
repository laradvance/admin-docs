# Column filter

> Since v.1.7.2

This feature sets a filter for the corresponding column in the header, which makes it easier to perform data table filtering based on this column.

![WX20190623-190256](https://user-images.githubusercontent.com/1479100/59975300-843cb380-95e9-11e9-9479-bf3f7329cffb.png)

There are three types of filters:

## String comparison query

```php
$grid->column('code')->filter();
```

The above call can add a `input` type filter to the header of the `code` column. Click the filter icon to expand the filter. After the query is submitted, the `equal` query will be executed for this column.

If you need a like query:

```php
$grid->column('title')->filter('like');
```

![WX20190623-192038](https://user-images.githubusercontent.com/1479100/59975592-3ecdb580-95ec-11e9-94f5-a6d5a33fb889.png)

If the field is a time, date related field, you can use the following method

```php
$grid->column('date')->filter('date');

$grid->column('time')->filter('time');

$grid->column('datetime')->filter('datetime');
```

## Multiple select query

Suppose you need to filter one or more status data in the table data through the `status` field. It is very convenient to use `Multiple select query`.

```php
$grid->column('status')->filter([
    0 => 'Unknown',
    1 => 'Ordered',
    2 => 'Paid',
    3 => 'Cancelled',
]);
```

![WX20190623-192234](https://user-images.githubusercontent.com/1479100/59975605-79cfe900-95ec-11e9-8d29-c4d169d9dcff.png)

## Range query

Suppose you need to filter out data in a price range by using the `price` field.

```php
$grid->column('price')->filter('range');
```

![WX20190623-192707](https://user-images.githubusercontent.com/1479100/59975636-de8b4380-95ec-11e9-82f9-ddd45d05152f.png)

Or filtering of time and date range

```php
$grid->column('date')->filter('range', 'date');

$grid->column('time')->filter('range', 'time');

$grid->column('datetime')->filter('range', 'datetime');
```

![WX20190623-192109](https://user-images.githubusercontent.com/1479100/59975593-3ecdb580-95ec-11e9-9cfc-32dbe27a175a.png)
