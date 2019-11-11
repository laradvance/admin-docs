# Model-show

> Since v1.5.16

`Encore\Admin\Show` is used to show the details of the data. Let's take an example. There is a `posts` table in the database:

```sql
CREATE TABLE `posts` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `author_id` int(10) unsigned NOT NULL ,
  `title` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `content` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `rate` int(255) COLLATE utf8_unicode_ci NOT NULL,
  `release_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
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
                
                $show->id('ID');
                $show->title('Title');
                $show->content();
                $show->rate();
                $show->created_at();
                $show->updated_at();
                $show->release_at();
                
            }));
        });
    }
}
```

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