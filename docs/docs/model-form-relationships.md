# Relationship

The model form supports data processing related to multiple models such as one-to-one, one-to-many, and many-to-many.

## One to one

The `users` table and the `profiles` table are associated one-to-one with the `profiles.user_id` field

```php
users
    id-integer
    name-string
    email-string

profiles
    id-integer
    user_id-integer
    age-integer
    gender-string
```

The corresponding data model is:

```php

class User extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
}

class Profile extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}

```

### HasOne

The following code can directly edit the age and gender fields in the profile of the associated model in the form of the User model

```php

$form = new Form(new User);

$form->text('name');
$form->text('email');

// Fields associated with model profile
$form->text('profile.age');
$form->text('profile.gender');

```

### BelongsTo

Similarly, the reverse relationship can also directly edit the data in the attribution model `User` in the form of the `Profile` model

```php

$form = new Form(new Profile);

$form->text('age');
$form->text('gender');

// Fields associated with model profile
$form->text('user.name');
$form->text('user.email');

```

### MorphOne

The usage method of `MorphOne` association is the same as the above `HasOne` and `BelongsTo` association.

## One to many

### HasMany

Two of `painters` and `paintings` establish a one-to-many relationship through `painter_id`:

```php
painters
  id-integer
  username-string
  bio-text

paintings
  id-integer
  painter_id-integer
  title-string
  body-text
  completed_at-timestamp
```

The model of the table is:

```php

class Painter extends Model
{
    public function paintings()
    {
        return $this->hasMany(Painting::class,'painter_id');
    }
}

class Painting extends Model
{
    public function painter()
    {
        return $this->belongsTo(Painter::class,'painter_id');
    }
}
```

Use the following form to build the code to edit the main table and field fields:

```php

// Main table field
$form->text('username')->rules('required');
$form->textarea('bio')->rules('required');

// Subtable fields
$form->hasMany('paintings', function (Form\NestedForm $form) {
    $form->text('title');
    $form->image('body');
    $form->datetime('completed_at');
});

// You can use the second parameter to set the label
$form->hasMany('paintings','Painting', function (Form\NestedForm $form) {

});
```

> Note: At present, HasMany forms are not friendly enough to support complex form components (rich text, etc.).

### MorphMany

The use of polymorphic one-to-many association and HasMany association is one-to-one

`posts` and `comments` have two polymorphic one-to-many associations via `painter_id`:

```php
posts
  id-integer
  content-text

comments
  id-integer
  body-integer
  commentable_id-integer
  commentable_type-string
```

The model of the table is:

```php

class Post extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class,'commentable');
    }
}

class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

Use the following form to build the code to edit the main table and field fields:

```php

// Main table field
$form->text('username')->rules('required');
$form->textarea('bio')->rules('required');

// Subtable fields
$form->morphMany('paintings', function (Form\NestedForm $form) {
    $form->text('title');
    $form->image('body');
    $form->datetime('completed_at');
});

// You can use the second parameter to set the label
$form->morphMany('paintings','Painting', function (Form\NestedForm $form) {

});
```

> In fact, the morphMany method is an alias of the hasMany method, and the distinction is made due to the different associated names. The two methods of use are the same.

## Many to many

### BelongsToMany

The `users` table and the `roles` table are related through the middle table `role_user` many-to-many, a user can have multiple roles, and a role can also belong to multiple users

```php
users
    id-integer
    name-string

roles
    id-integer
    name-integer

role_user
    user_id-integer
    role_id-integer
```

The corresponding data model is:

```php

class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

class Role extends Model
{
    public function user()
    {
        return $this->belongsToMany(User::class);
    }
}

```

Then there are two components `multipleSelect` and `checkbox` which support the selection of `many to many association relationship`

For example, select the role in the user's form:

```php
$form = new Form(new User);

$form->multipleSelect('roles','Role')->options(Role::all()->pluck('name','id'));
```

Or select the user in the role form:

```php
$form = new Form(new Role);

$form->multipleSelect('users','User')->options(User::all()->pluck('name','id'));
```

The same usage of `checkbox`:

```php
$form = new Form(new User);

$form->checkbox('roles','role')->options(Role::all()->pluck('name','id'));
```

## BelongsTo selection

The attribution relationship selection is a new function of `v1.8.0`, and it is also a more commonly used scenario.

Take the following article table and user table as examples, each article belongs to a user (author)

```php
articles
    id-integer
    author_id-integer
    title-string
    body-text

users
    id-integer
    name-string
    email-string
    avatar-string
```

model:
```php
class Article extends Model
{
    public function author()
    {
        return $this->belongsTo(User::class);
    }
}

class User extends Model
{
}
```

If you want to select the author in the form of the article, you can use the `select` component

```php
$form->select('author_id')->options(User::all()->pluck('name','id'));
```

There are two obvious problems with the `select` component. First, if there are too many users, the selected option will be too long. Second, there is no way to display more user information in the selected option, such as `email`, The two fields "avatar".

The following two methods `BelongsTo` and `BelongsToMany` solve this problem very well, using the form of `popup list` to select the attribution object.

### BelongsTo

Also use the table structure and model in the above example, using the following method

First define the list selection class:

```php
<?php

namespace App\Admin\Selectable;

use App\Models\User;
use Encore\Admin\Grid\Filter;
use Encore\Admin\Grid\Selectable;

class Users extends Selectable
{
    public $model = User::class;

    public function make()
    {
        $this->column('id');
        $this->column('name');
        $this->column('email');
        $this->column('avatar','Avatar')->image();
        $this->column('created_at');

        $this->filter(function (Filter $filter) {
            $filter->like('name');
        });
    }
}
```

In the list selection class, the `$model` attribute is used to specify the model of the list. The default data of the list is `10`. You can use the attribute `protected $perPage = 5;` to set the number of each page.

The `make` method is used to build the list, please refer to [model-grid documentation](/docs/zh/model-grid.md)

Here is how to use it in the form:

```php
use App\Admin\Selectable\Users;

$form->belongsTo('author_id', Users::class,'Author');
```

The effect is as follows:

![Kapture 2020-05-21 at 18 08 28](https://user-images.githubusercontent.com/1479100/82548684-43994180-9b8e-11ea-949e-23ff0323347c.gif)

### BelongsToMany

Use `belongsToMany` method instead of `multipleSelect` to select many-to-many relationship

Take the following article table and user table as examples, each article belongs to multiple users (collaborators)

```php
articles
    id-integer
    title-string
    body-text

users
    id-integer
    name-string
    email-string
    avatar-string

article_user
    user_id-integer
    article_id-integer
```

model:
```php
class Article extends Model
{
    public function collaborators()
    {
        return $this->belongsToMany(User::class);
    }
}

class User extends Model
{
}
```

Here is how to use it in the form:

```php
// Select the class using the list defined in the example above
use App\Admin\Selectable\Users;

$form->belongsToMany('collaborators', Users::class, __('Collaborators'));
```

The effect is as follows:

![belongstomany](https://user-images.githubusercontent.com/1479100/82548917-b3a7c780-9b8e-11ea-8025-4cbfde209dd8.gif)


### Use in the list page

As long as the attribution selector is used in the form, it can also be used in the list page to achieve in-line editing

```php
// Select the class using the list defined in the example above
use App\Admin\Selectable\Users;

$grid->column('author_id','Author')
    ->belongsTo(Users::class);

$grid->column('collaborators','Collaborators')
    ->belongsToMany(Users::class);
```

Note that when used in a list, **must** add a `display` method to the list selection class `App\Admin\Selectable\Users` to define the display of these two columns in the list:

```php
class Users extends Selectable
{
    ...

    public static function display()
    {
        return function ($value) {
            
            // If `$value` is an array, it means it is used in the `collaborators` column, and the user’s name field separated by a semicolon `;` is displayed
            if (is_array($value)) {
                return implode(';', array_column($value,'name'));
            }

            // Otherwise it is used in the `author_id` column, which directly displays the user’s `name` field
            return optional($this->author)->name;
        };
    }
}

```

The effect is as follows:

![belongsto-inline](https://user-images.githubusercontent.com/1479100/82550220-c28f7980-9b90-11ea-88b3-f64108956c1e.gif)

## Unsupported relationship

The following types of relationship do not supported

`HasOneThrough`, `HasManyThrough`, `MorphTo`, `MorphToMany`
