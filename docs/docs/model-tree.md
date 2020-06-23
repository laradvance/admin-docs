# Model-tree

Can be achieved through a `model-tree` to a tree-like components, you can drag the way to achieve the level of data, sorting and other operations, the following is the basic usage.

## Table structure and model

To use `model-tree`, you have to follow the convention of the table structure:

```sql
categories
    id          - integer
    parent_id   - integer
    order       - integer
    title       - string
```

The above table structure has three necessary fields `parent_id`, `order`, `title`, and the other fields are not required.

The corresponding model is `app/Models/Category.php`:

```php
<?php

namespace App\Models\Demo;

use Encore\Admin\Traits\AdminBuilder;
use Encore\Admin\Traits\ModelTree;
use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    use ModelTree, AdminBuilder;

    protected $table = 'demo_categories';
}
```

Table structure in the three fields `parent_id`,`order`, `title` field name can be amended:

```php
<?php

namespace App\Models\Demo;

use Encore\Admin\Traits\AdminBuilder;
use Encore\Admin\Traits\ModelTree;
use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    use ModelTree, AdminBuilder;

    protected $table = 'demo_categories';

    public function __construct(array $attributes = [])
    {
        parent::__construct($attributes);

        $this->setParentColumn('pid');
        $this->setOrderColumn('sort');
        $this->setTitleColumn('name');
    }
}
```

## Usage

Then use `model-tree` in your page

```php
<?php

namespace App\Admin\Controllers\Demo;

use App\Http\Controllers\Controller;
use App\Models\Category;
use Encore\Admin\Form;
use Encore\Admin\Facades\Admin;
use Encore\Admin\Layout\Content;
use Encore\Admin\Controllers\ModelForm;
use Encore\Admin\Tree;

class CategoryController extends Controller
{
    use ModelForm;

    public function index()
    {
        return Admin::content(function (Content $content) {
            $content->header('Categories');
            $content->body(Category::tree());
        });
    }
}
```

You can modify the display of branch in the following ways:

```php
Category::tree(function ($tree) {
    $tree->branch(function ($branch) {
        $src = config('admin.upload.host') . '/' . $branch['logo'] ;
        $logo = "<img src='$src' style='max-width:30px;max-height:30px' class='img'/>";

        return "{$branch['id']} - {$branch['title']} $logo";
    });
})
```

The `$branch` parameter is array of current row data.

If you want to modify the query of the model, use the following way:

```php

Category::tree(function ($tree) {

    $tree->query(function ($model) {
        return $model->where('type', 1);
    });

})
```
