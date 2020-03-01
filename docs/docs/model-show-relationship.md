# Show relationships

## One-to-one relationship

The `users` table and the above `posts` table are one-to-one associations, which are associated by the `posts.author_id` field. The `users` table structure is as follows:

```sql
CREATE TABLE `users` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `email` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

The model is defined as:
```php
class User extends Model
{
}

class Post extends Model
{
    public function author()
    {
        return $this->belongsTo(User::class, 'author_id');
    }
}
```

Then you can show the details of the user to which `post` belongs in the following way:
```php
$show->author('Author information', function ($author) {

    $author->setResource('/admin/users');

    $author->id();
    $author->name();
    $author->email();
});
```
The `$author` object is also a `Show` instance. You can also use the various methods above.

> Note: In order to be able to use the tool in the upper right corner of this panel, you must set the url access path of the user resource with the `setResource()` method.

## One-to-many or many-to-many relationship

The associated data for a one-to-many or many-to-many relationship will be presented as `Model-grid`. Below is a simple example.

The `posts` table and the comment table `comments` are one-to-many relationships (a `post` has multiple `comments`), associated with the `comments.post_id` field.

```sql
CREATE TABLE `comments` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `post_id` int(10) unsigned NOT NULL,
  `content` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci
```

The model is defined as:
```php
class Post extends Model
{
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

class Comment extends Model
{
}

```

Then the display of the comment is implemented by the following code:

```php
$show->comments('Comments', function ($comments) {

    $comments->resource('/admin/comments');

    $comments->id();
    $comments->content()->limit(10);
    $comments->created_at();
    $comments->updated_at();

    $comments->filter(function ($filter) {
        $filter->like('content');
    });
});

```

`$comments` is an instance of `Encore\Admin\Grid`. For detailed usage, please refer to [model-grid](/zh/model-grid.md)

> Note: In order to be able to use this data table function normally, you must use the `resource()` method to set the url access path of the `comments` resource.