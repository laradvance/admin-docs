# Model-show

> Since v1.5.16

`Encore\Admin\Show` is used to show the details of the data. Let's take an example. There is a `posts` table in the database:

```sql
posts
    id          - integer
    author_id   - integer
    content     - text
    title       - string
    content     - text
    rate        - integer
    release_at  - timestamp
```

The corresponding data model is `App\Models\Post`, and the following code can show the data details of the `posts` table:

```php
<?php

namespace App\Admin\Controllers;

use App\Http\Controllers\Controller;
use App\Models\Post;
use Encore\Admin\Facades\Admin;
use Encore\Admin\Layout\Content;
use Encore\Admin\Show;

class PostController extends Controller
{
    public function show($id)
    {
        return Admin::content(function (Content $content) use ($id) {

            $content->header('Post');
            $content->description('Detail');

            $content->body(Admin::show(Post::findOrFail($id), function (Show $show) {

                $show->field('id', 'ID');
                $show->field('title', 'Title');
                $show->field('content');
                $show->field('rate');
                $show->field('created_at');
                $show->field('updated_at');
                $show->field('release_at');

            }));
        });
    }
}
```

> If there is no detail method in your controller, refer to the above code and add this method

If you want to show all the fields directly, you can use the following simple method:

```php
$content->body(Admin::show(Post::findOrFail($id)));
```

If you want to show the specified field directly:

```php
$content->body(Admin::show(Post::findOrFail($id), ['id', 'title', 'content']));
```

Or specify the label for each field:

```php
$content->body(Admin::show(Post::findOrFail($id), [
    'id'        => 'ID',
    'title'     => 'Title',
    'content'   => 'Contents'
]));
```

## Basic usage

### Escape content

In order to prevent XSS attacks, the default output content will be HTML escape, if you don't want to escape the output HTML, you can call the `unescape` method:

```php
$show->avatar()->unescape()->as(function ($avatar) {

     return "<img src='{$avatar}' />";

});
```

### Modify the style and title of the panel

```php
$show->panel()
    ->style('danger')
    ->title('post detail...');
```

The value of `style` could be `primary`, `info`, `danger`, `warning`, `default`

### Panel tool settings

There are three buttons `Edit`, `Delete`, `List` in the upper right corner of the panel. You can turn them off in the following ways:

```php
$show->panel()
    ->tools(function ($tools) {
        $tools->disableEdit();
        $tools->disableList();
        $tools->disableDelete();
    });;
```
