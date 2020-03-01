# Data export

The built-in export function of `model-grid` only implements the export of simple csv format files. If you encounter file coding problems or can not meet your own needs, you can follow the steps below to customize the export function.

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