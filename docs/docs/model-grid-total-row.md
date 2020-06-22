# Model grid total row

> since v1.6.13

## Basic usage

If the value of a column field is a number and it needs to be displayed at the bottom of the table after statistics, you can use the following method.

```php
$grid->column('manager_name')->totalRow('Total:');

//for counting rows
$grid->column('expand_orders')->totalCount();

//for counting sum
$grid->column('orders_all_sum')->totalSum();

//for counting average
$grid->column('orders_approved_avg')->totalAvg();//for counting average
```

Call the `totalRow()` method after the corresponding field.

## Parameters

After calling the `totalRow()` method on this column, the `sum` method of `Eloquent` is called by default to accumulate the value display of this column, and the `totalRow()` method can accept an optional parameter if it is passed in. The parameter is a string or a number, then it will be displayed directly at the bottom of the total row, for example, the word "Total" is displayed directly under the `id` column:

```php
$grid->column('id', 'ID')->sortable()->totalRow('Total');
```

If you want to change the display style of a total row, you can modify it by passing in an anonymous function:

```php
$grid->column('amount')->totalRow(function ($amount) {

    return "<span class='text-danger text-bold'><i class='fa fa-yen'></i> {$amount} </span>"

});
```
