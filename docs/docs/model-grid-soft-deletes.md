# Soft Deleting

> Since v1.7.9

First refer to the Laravel documentation to implement [soft delete](https://laravel.com/docs/5.8/eloquent#soft-deleting) of the model:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;
}
```

In this way, the data displayed in the grid list is data that has not been deleted.

```php
$grid = new Grid(new Post);

$grid->id('ID')->sortable();
$grid->title('Title');
$grid->created_at('Created at');
$grid->updated_at('Updated at');

return $grid;
```

## Recycle Bin

Next we need to add an entry that allows us to see the soft deleted data. Here we can use the scope filter of model-grid

```php
$grid->filter(function() {

    // Range filter, call the model's `onlyTrashed` method to query the soft deleted data.
    $filter->scope('trashed', 'Recycle Bin')->onlyTrashed();
    
});
```

A “Recycle Bin” button will appear in the drop-down menu of the filter button in the table header. Clicking it will call the model ’s `onlyTrashed` method to query the deleted data from the table, that is, the data in the Recycle Bin .

![WX20191220-105515](https://user-images.githubusercontent.com/1479100/71235280-add75d00-2336-11ea-97f4-bb8d3f65b20c.png)

## Row restore

According to the following method, we can add a restore action to each row of data in the recycle bin to facilitate data recovery

At first we define the action class `app/Admin/Actions/Post/Restore.php`:

```php
<?php

namespace App\Admin\Actions\Post;

use Encore\Admin\Actions\RowAction;
use Illuminate\Database\Eloquent\Model;

class Restore extends RowAction
{
    public $name = 'Restore';

    public function handle (Model $model)
    {
        $model->restore();

        return $this->response()->success('Restore')->refresh();
    }

    public function dialog()
    {
        $this->confirm('Are you sure you want to restore?');
    }
}
```

Add to row actions:

```php
use App\Admin\Actions\Post\Restore;

$grid->actions (function ($actions) {

    if (\ request('_ scope_') == 'trashed') {
        $actions->add(new Restore());
    }

});
```

## Batch recovery operation

First define the action class `app/Admin/Actions/Post/BatchRestore.php`:

```php
<?php

namespace App\Admin\Actions\Post;

use Encore\Admin\Actions\BatchAction;
use Illuminate\Database\Eloquent\Collection;

class BatchRestore extends BatchAction
{
    public $name = 'Restore';

    public function handle (Collection $collection)
    {
        $collection->each->restore();

        return $this->response()->success('Recovered')->refresh();
    }

    public function dialog ()
    {
        $this->confirm('Are you sure you want to resume?');
    }
}
```

Add to batch operation:

```php
use App\Admin\Actions\Post\BatchRestore;

$grid->batchActions (function($batch) {

    if (\request('_scope_') == 'trashed') {
        $batch->add(new BatchRestore());
    }
    
});
```