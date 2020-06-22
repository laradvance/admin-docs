# External data source

## The data comes from the model

If you use a model to obtain data, then modifying the source data is very simple:

```php
// Add default query condition
$grid->model()->where('id','>', 100);

// Set the initial sort condition
$grid->model()->orderBy('id','desc');

...
```

For other query methods, please refer to the query method of `eloquent`.

## Data from external API

Since the data of `model-grid` depends on `Eloquent model` to query and obtain, so if your list data is not from the database but the external API, then you can not directly use the conventional method to process.

Because there is no unified way to obtain external data, laravel-admin does not encapsulate a unified method to handle external data, but we can achieve data reading, querying, sorting and other operations by covering some common methods of `Eloquent model`.

The following example uses `Douban` Movie's API to obtain and display data:

```php
<?php

namespace App\Models\Movie;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Support\Facades\Request;

class InTheater extends Model
{
    public function paginate()
    {
        $perPage = Request::get('per_page', 10);

        $page = Request::get('page', 1);

        $start = ($page-1)*$perPage;

        $data = file_get_contents("https://api.douban.com/v2/movie/in_theaters?city=上海&start=$start&count=$perPage");

        $data = json_decode($data, true);

        extract($data);

        $movies = static::hydrate($subjects);

        $paginator = new LengthAwarePaginator($movies, $total, $perPage);

        $paginator->setPath(url()->current());

        return $paginator;
    }

    public static function with($relations)
    {
        return new static;
    }

    // Override `orderBy` to collect sorted fields and directions
    public function orderBy($column, $direction = 'asc')
    {

    }


    // Overwrite `where` to collect filtered fields and conditions
    public function where($column, $operator = null, $value = null, $boolean = 'and')
    {

    }

    ...
}
```

Get API data by overriding the `paginate` and `with` methods of the model, and get single data by `findOrFail` method and display it in the form

Similarly, if you want to get or save data in the form, you can also achieve it by overriding the corresponding method:

```php

// Get single item data and display it in the form
public function findOrFail($id)
{
    $data = file_get_contents("http://api.douban.com/v2/movie/subject/$id");

    $data = json_decode($data, true);

    return static::newFromBuilder($data);
}

// Save the submitted form data
public function save(array $options = [])
{
    $attributes = $this->getAttributes();

    // save $attributes
}
```

## Data comes from complex SQL queries

If the source data needs to be obtained by executing more complex SQL statements, then there are two methods. The first method is the above method, which overrides the model method.

```php
public function paginate()
{
    $perPage = Request::get('per_page', 10);

    $page = Request::get('page', 1);

    $start = ($page-1)*$perPage;


    // Run sql to get the data array
    $sql = 'select * from ...';

    $result = DB::select($sql);

    $movies = static::hydrate($result);

    $paginator = new LengthAwarePaginator($movies, $total, $perPage);

    $paginator->setPath(url()->current());

    return $paginator;
}

public static function with($relations)
{
    return new static;
}
```

The second way is to establish a view and model binding in the database (untested, theoretically feasible)
