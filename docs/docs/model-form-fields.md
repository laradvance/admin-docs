# Builtin form fields

There are a lots of form components built into the `model-form` to help you quickly build forms.

## Public methods

### Set the value to save

```php
$form->text('title')->value('text...');
```

### Set default value

```php
$form->text('title')->default('text...');
```

### Set help message

```php
$form->text('title')->help('help...');
```

### Set attributes of field element

```php
$form->text('title')->attribute(['data-title' => 'title...']);

$form->text('title')->attribute('data-title', 'title...');
```

### Set placeholder

```php
$form->text('title')->placeholder('Please input...');
```

### Set required

```php
$form->text('title')->required();
```

### Setting pattern

```php
$form->text('title')->pattern('[A-z]{3}');
```

### Setting readonly

```php
$form->text('title')->readonly();
```

### Setting disable

```php
$form->text('title')->disable();
```

### Setting autofocus

```php
$form->text('title')->autofocus();
```

### Model-form-tab

If the form contains too many fields, will lead to form page is too long, in which case you can use the tab to separate the form:

```php

$form->tab('Basic info', function ($form) {

    $form->text('username');
    $form->email('email');

})->tab('Profile', function ($form) {

   $form->image('avatar');
   $form->text('address');
   $form->mobile('phone');

})->tab('Jobs', function ($form) {

     $form->hasMany('jobs', function () {
         $form->text('company');
         $form->date('start_date');
         $form->date('end_date');
     });

  })

```

## Text input

![text](https://user-images.githubusercontent.com/1479100/82288328-d3938b80-99d4-11ea-91ec-4915d48d6057.png)

```php
$form->text($column, [$label]);

// Add a submission validation rule
$form->text($column, [$label])->rules('required|min:10');

// Set FontAwesome icon
$form->text($column, [$label])->icon('fa-pencil');

// Set datalist
$form->text($column, [$label])->datalist(['key' => 'value']);

// Set inputmask, see https://github.com/RobinHerbots/Inputmask
$form->text('code')->inputmask(['mask' => '99-9999999']);
```

## Textarea

![textarea](https://user-images.githubusercontent.com/1479100/82288329-d3938b80-99d4-11ea-9066-41e163824995.png)

```php
$form->textarea($column[, $label])->rows(10);
```

## Radio

![radio](https://user-images.githubusercontent.com/1479100/82288325-d1c9c800-99d4-11ea-8403-90b0b73526bf.png)

```php
$form->radio($column[, $label])->options(['m' => 'Female', 'f'=> 'Male'])->default('m');

$form->radio($column[, $label])->options(['m' => 'Female', 'f'=> 'Male'])->default('m')->stacked();
```

## Checkbox

![checkbox](https://user-images.githubusercontent.com/1479100/82288312-cd051400-99d4-11ea-86cb-dc1f36c1f1a5.png)

`checkbox` can store values in two ways, see[multiple select](#Multiple select)

The `options()` method is used to set options:

```php
$form->checkbox($column[, $label])->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);

$form->checkbox($column[, $label])->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name'])->stacked();

// Setting options through closures
$form->checkbox($column[, $label])->options(function () {
    return [1 => 'foo', 2 => 'bar', 'val' => 'Option name'];
});

// If there are too many options, you can add a full checkbox to it.
$form->checkbox($column[, $label])->options([])->canCheckAll();
```

## Select

![select](https://user-images.githubusercontent.com/1479100/82288327-d2faf500-99d4-11ea-9b34-68386b1ebaf6.png)

```php
$form->select($column[, $label])->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);
```

If have too many options, you can load option by ajax:

```php
$form->select('user_id')->options(function ($id) {
    $user = User::find($id);

    if ($user) {
        return [$user->id => $user->name];
    }
})->ajax('/admin/api/users');
```

The controller method for api `/admin/api/users` is:

```php
public function users(Request $request)
{
    $q = $request->get('q');

    return User::where('name', 'like', "%$q%")->paginate(null, ['id', 'name as text']);
}

```

The json returned from api `/admin/demo/options`:

```json
{
  "total": 4,
  "per_page": 15,
  "current_page": 1,
  "last_page": 1,
  "next_page_url": null,
  "prev_page_url": null,
  "from": 1,
  "to": 3,
  "data": [
    {
      "id": 9,
      "text": "xxx"
    },
    {
      "id": 21,
      "text": "xxx"
    },
    {
      "id": 42,
      "text": "xxx"
    },
    {
      "id": 48,
      "text": "xxx"
    }
  ]
}
```

### Cascading select

`select` component supports one-way linkage of parent-child relationship:

```php
$form->select('province')->options(...)->load('city', '/api/city');

$form->select('city');

```

Where `load('city', '/api/city');` means that, after the current select option is changed, the current option will call the api `/api/city` via the argument`q` api returns the data to fill the options for the city selection box, where api `/api/city` returns the data format that must match:

```json
[
    {
        "id": 1,
        "text": "foo"
    },
    {
        "id": 2,
        "text": "bar"
    },
    ...
]
```

The code for the controller action is as follows:

```php
public function city(Request $request)
{
    $provinceId = $request->get('q');

    return ChinaArea::city()->where('parent_id', $provinceId)->get(['id', DB::raw('name as text')]);
}
```

## Multiple select

![mselect](https://user-images.githubusercontent.com/1479100/82288323-d1313180-99d4-11ea-83f3-16c192e30ec2.png)

```php
$form->multipleSelect($column[, $label])->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);
```

You can store value of multiple select in two ways, one is `many-to-many` relation.

```php

class Post extends Models
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}

$form->multipleSelect('tags')->options(Tag::all()->pluck('name', 'id'));

```

The second is to store the option array into a single field. If the field is a string type, it is necessary to define [accessor and Mutator](https://laravel.com/docs/5.5/eloquent-mutators) for the field.

For example, the field `tags` is stored as a string and separated by a comma `,`, then its accessors and modifiers are defined as follows:

```php

class Post extends Model

    public function getTagsAttribute($value)
    {
        return explode(',', $value);
    }

    public function setTagsAttribute($value)
    {
        $this->attributes['tags'] = implode(',', $value);
    }
}
```

If have too many options, you can load option by ajax

```php
$form->select('user_id')->options(function ($id) {
    $user = User::find($id);

    if ($user) {
        return [$user->id => $user->name];
    }
})->ajax('/admin/api/users');
```

The controller method for api `/admin/api/users` is:

```php
public function users(Request $request)
{
    $q = $request->get('q');

    return User::where('name', 'like', "%$q%")->paginate(null, ['id', 'name as text']);
}

```

The json returned from api `/admin/demo/options`:

```json
{
  "total": 4,
  "per_page": 15,
  "current_page": 1,
  "last_page": 1,
  "next_page_url": null,
  "prev_page_url": null,
  "from": 1,
  "to": 3,
  "data": [
    {
      "id": 9,
      "text": "xxx"
    },
    {
      "id": 21,
      "text": "xxx"
    },
    {
      "id": 42,
      "text": "xxx"
    },
    {
      "id": 48,
      "text": "xxx"
    }
  ]
}
```

## Listbox

![QQ20200519-130525](https://user-images.githubusercontent.com/1479100/82287107-f8d2ca80-99d1-11ea-90a2-8efa9c5ff224.png)

The usage is as same as mutipleSelect.

```php
$form->listbox($column[, $label])->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);

// Set height
$form->listbox($column[, $label])->height(200);
```

## Email input

```php
$form->email($column[, $label]);
```

## Password input

![password](https://user-images.githubusercontent.com/1479100/82288324-d1c9c800-99d4-11ea-9085-c510ab007a20.png)

```php
$form->password($column[, $label]);
```

## URL input

![URL](https://user-images.githubusercontent.com/1479100/82288331-d42c2200-99d4-11ea-9e68-39eb6341afdc.png)

```php
$form->url($column[, $label]);
```

## Ip input

![ip](https://user-images.githubusercontent.com/1479100/82288319-d0000480-99d4-11ea-96d8-92d8c7e7f1fe.png)

```php
$form->ip($column[, $label]);
```

## Phone number input

![mobile](https://user-images.githubusercontent.com/1479100/82288321-d0989b00-99d4-11ea-847a-818ce7d419db.png)

```php
$form->mobile($column[, $label])->options(['mask' => '999 9999 9999']);
```

## Color selector

![color](https://user-images.githubusercontent.com/1479100/82288314-ce364100-99d4-11ea-9fd6-f31991f013ad.png)

```php
$form->color($column[, $label])->default('#ccc');
```

## Time input

```php
$form->time($column[, $label]);

// Set the time format, more formats reference http://momentjs.com/docs/#/displaying/format/
$form->time($column[, $label])->format('HH:mm:ss');
```

## Date input

![datetime](https://user-images.githubusercontent.com/1479100/82288315-ceced780-99d4-11ea-98cc-cb332016137d.png)

```php
$form->date($column[, $label]);

// Date format setting,more format please see http://momentjs.com/docs/#/displaying/format/
$form->date($column[, $label])->format('YYYY-MM-DD');
```

## Datetime input

```php
$form->datetime($column[, $label]);

// Set the date format, more format reference http://momentjs.com/docs/#/displaying/format/
$form->datetime($column[, $label])->format('YYYY-MM-DD HH:mm:ss');
```

## Time range select

`$startTime`、`$endTime`is the start and end time fields:

```php
$form->timeRange($startTime, $endTime, 'Time Range');
```

## Date range select

`$startDate`、`$endDate`is the start and end date fields:

```php
$form->dateRange($startDate, $endDate, 'Date Range');
```

## Datetime range select

`$startDateTime`、`$endDateTime` is the start and end datetime fields:

```php
$form->datetimeRange($startDateTime, $endDateTime, 'DateTime Range');
```

## Currency input

```php
$form->currency($column[, $label]);

// set the unit symbol
$form->currency($column[, $label])->symbol('￥');

```

## Number input

```php
$form->number($column[, $label]);

// set max value
$form->number($column[, $label])->max(100);

// set min value
$form->number($column[, $label])->min(10);
```

## Rate input

```php
$form->rate($column[, $label]);
```

## Slider

Can be used to select the type of digital fields, such as age:

```php
$form->slider($column[, $label])->options(['max' => 100, 'min' => 1, 'step' => 1, 'postfix' => 'years old']);
```

More options please ref to https://github.com/IonDen/ion.rangeSlider#settings

## Rich text editor

The rich text editing component is removed after v1.7.0 version, please choose to use the following rich text editor extension:

| Extension   | URL                                                     |
| ----------- | ------------------------------------------------------- |
| wangEditor  | https://github.com/laravel-admin-extensions/wangEditor  |
| wangEditor2 | https://github.com/laravel-admin-extensions/wangEditor2 |
| UEditor     | https://github.com/laravel-admin-extensions/UEditor     |
| Summernote  | https://github.com/laravel-admin-extensions/summernote  |
| Quill       | https://github.com/laravel-admin-extensions/quill       |
| CKEditor    | https://github.com/laravel-admin-extensions/ckeditor    |
| Simditor    | https://github.com/laravel-admin-extensions/simditor    |

## Hidden field

```php
$form->hidden($column);
```

## Switch

`On` and `Off` pairs of switches with the values `1` and`0`:

```php
$states = [
    'on'  => ['value' => 1, 'text' => 'enable', 'color' => 'success'],
    'off' => ['value' => 0, 'text' => 'disable', 'color' => 'danger'],
];

$form->switch($column[, $label])->states($states);
```

## Latitude and longitude selection

> The map component is removed after v1.7.0 version, please use the selector [Latitude and longitude selector plugin extension](https://github.com/laravel-admin-extensions/latlong) instead.

## Display field

Only display the fields and without any action:

```php
$form->display($column[, $label]);

// More complex display
$form->display($column[, $label])->with(function ($value) {
    return "<img src="$value" />";
});
```

## Divider

```php
$form->divider();

// OR
$form->divider('Title');

//divider with description
$form->divider('Some long words')->note();
```

## Html

insert html,the argument passed in could be objects which impletements `Htmlable`、`Renderable`, or has method `__toString()`

```php
$form->html('html contents'[, $label]);
```

## Tags

Insert the comma (,) separated string `tags`

```php
$form->tags('keywords'[, $label]);
```

## Icon

![icon](https://user-images.githubusercontent.com/1479100/82288317-cf676e00-99d4-11ea-92c5-a393bd4dfb64.png)

Select the `font-awesome` icon.

```php
$form->icon('icon'[, $label]);
```

## Timezone

```php
$form->timezone('timezone'[, $label]);
```
