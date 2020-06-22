# Data export

`model-grid` has built-in export of csv format files. By default, the data displayed in the list is exported to csv format files.

> since v1.7.16

Using the default csv export, there are the following methods available.

```php
$grid->export(function ($export) {
 
    $export->filename('Filename.csv');

    $export->except(['column1', 'column2' ...]);

    $export->only(['column3', 'column4' ...]);

    $export->originalValue(['column1', 'column2' ...]);

    $export->column('column_5', function ($value, $original) {
        return $value;
    )};
});
```

`$export->filename($filename);` is used to specify the name of the exported file, if not set, the default is `table name.csv`

`$export->except([]);` is used to specify which columns do not need to be exported. After specified, the related columns will not be exported, otherwise, use `$export->only([]);` The method is used to specify which columns can only be exported.

In many cases, certain columns will be modified and displayed on the page. For example, after using the `$grid->column('name')->label()` method on the column, the content of the exported column will be a piece of HTML, If you need some columns to export the original content stored in the database, use the `originalValue` method.

`` `php
$export->originalValue(['name']);
`` `

Finally, if you want to customize the export content of certain columns, use the `column` method

```php
$export->column('column_5', function ($value, $original) {
    // return $value;
)};
```

Among them, `$value` and `$original` passed into the closure function are the original value of the column, and the modified value after applying certain methods. You can implement your own logic in the closure function.

## Laravel-Excel v3.*

Export support for the `Laravel-Excel 3.*` version has been added after the `v1.6.12` version.

First install [Laravel-Excel](https://github.com/Maatwebsite/Laravel-Excel) according to the documentation.

Then create an export class:

```php
<?php

Namespace App\Admin\Extensions;

Use Encore\Admin\Grid\Exporters\ExcelExporter;

class PostsExporter extends ExcelExporter
{
    protected $fileName = 'Article list.xlsx';

    protected $columns = [
        'id' => 'ID',
        'title' => 'title',
        'content' => 'content',
    ];
}
```

Then use this export class in the Grid:

```php

Use App\Admin\Extensions\PostsExporter;

$grid->exporter(new PostsExporter());
```

With the `$columns` setting above, only three specified three fields will be exported when exporting. If you want to export all the fields, specify the name of each field in order.

```php
class PostsExporter extends ExcelExporter
{
    protected $fileName = 'Article list.xlsx';

    protected $headings = ['ID', 'title', 'content' ... ];
}
```

## Laravel-Excel v2.*

This example uses [Laravel-Excel](https://github.com/Maatwebsite/Laravel-Excel) as an excel library, and of course you can use any other excel library.

First install it:

```shell
composer require maatwebsite/excel:~2.1.0

php artisan vendor:publish --provider="Maatwebsite\Excel\ExcelServiceProvider"
```

And then create a new custom export class, such as `app/Admin/Extensions/ExcelExpoter.php`:

```php
<?php

namespace App\Admin\Extensions;

use Encore\Admin\Grid\Exporters\AbstractExporter;
use Maatwebsite\Excel\Facades\Excel;

class ExcelExpoter extends AbstractExporter
{
    public function export()
    {
        Excel::create('Filename', function($excel) {

            $excel->sheet('Sheetname', function($sheet) {

                // This logic get the columns that need to be exported from the table data
                $rows = collect($this->getData())->map(function ($item) {
                    return array_only($item, ['id', 'title', 'content', 'rate', 'keywords']);
                });

                $sheet->rows($rows);

            });

        })->export('xls');
    }
}
```

And then use this class in `model-grid`:

```php

use App\Admin\Extensions\ExcelExpoter;

$grid->exporter(new ExcelExpoter());

```

For more information on how to use `Laravel-Excel`, refer to [laravel-excel/docs](http://www.maatwebsite.nl/laravel-excel/docs)
