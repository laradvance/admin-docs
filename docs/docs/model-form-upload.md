# File/Image upload

You need to complete the upload configuration before using the image upload function. Please read the configuration section of the document on this page.

## Upload image

Using local upload, the image upload directory is configured in `upload.image` in the file `config/admin.php`. If the directory does not exist, you need to create the directory and open the write permission.

Various methods such as compression, cropping, and watermarking can be used, and you need to install [intervention/image](http://image.intervention.io/getting_started/installation) first.

For more usage methods, please refer to [[Intervention](http://image.intervention.io/getting_started/introduction)]:

```php
$form->image($column[, $label]);

// Modify the image upload path and file name
$form->image($column[, $label])->move($dir, $name);

// crop the picture
$form->image($column[, $label])->crop(int $width, int $height, [int $x, int $y]);

// add watermark
$form->image($column[, $label])->insert($watermark,'center');

// Add picture delete button
$form->image($column[, $label])->removable();

// keep pictures when deleting data
$form->image($column[, $label])->retainable();

// Add a download button, click to download
$form->image($column[, $label])->downloadable();
```

Generate thumbnails after uploading pictures, Since v1.7.2

```php
// Generate thumbnails while uploading pictures
$form->image($column[, $label])->thumbnail('small', $width = 300, $height = 300);

// or multiple thumbnails
$form->image($column[, $label])->thumbnail([
    'small' => [100, 100],
    'small' => [200, 200],
    'small' => [300, 300],
]);
```

Use thumbnails in models

```php
class Photo extends Model
{
    use \Encore\Admin\Traits\Resizable;
}

// To access thumbnail
$photo->thumbnail('small','photo_column');
```

## File Upload

Using local upload, the file upload directory is configured in `upload.file` in the file `config/admin.php`. If the directory does not exist, you need to create the directory and open the write permission.

```php
$form->file($column[, $label]);

// Modify the file upload path and file name
$form->file($column[, $label])->move($dir, $name);

// and set the upload file type
$form->file($column[, $label])->rules('mimes:doc,docx,xlsx');

// Add file delete button
$form->file($column[, $label])->removable();

// Keep files when deleting data
$form->file($column[, $label])->retainable();

// Add a download button, click to download
$form->file($column[, $label])->downloadable();
```

## Multi-picture/file upload

```php
// Multi-map
$form->multipleImage($column[, $label]);

// Add delete button
$form->multipleImage($column[, $label])->removable();

// multiple files
$form->multipleFile($column[, $label]);

// Add delete button
$form->multipleFile($column[, $label])->removable();

// draggable sorting since v1.6.12
$form->multipleImage('pictures')->sortable();
```

The data submitted when uploading multiple pictures/files is an array of file paths, which can be directly stored in the `JSON` type field of mysql.

If you use mongodb, you can also store directly, but if you use the string type to store, you need to specify the data storage format,

For example, if you want to use json strings to store file data, you need to define modifiers and accessors for the fields in the model, for example, the field name is `pictures`:

```php
public function setPicturesAttribute($pictures)
{
    if (is_array($pictures)) {
        $this->attributes['pictures'] = json_encode($pictures);
    }
}

public function getPicturesAttribute($pictures)
{
    return json_decode($pictures, true);
}
```

Of course, you can also specify any other format.

### One-to-many relationship processing

> Since v1.8.0

Assuming that each article in the articles table has multiple attachments, and the attachments are stored in the `path` field of the attachments table, then it can be handled as follows

```php
articles
    id-integer
    title-string

attachments
    id-integer
    path-string
```

The model is:

```php
class Article extends Model
{
    public function attachments()
    {
        return $this->hasMany(Attachment::class);
    }
}

class Attachment extends Model
{
}
```

Use in form:

```php
$form->multipleFile('attachments','Attachments')->pathColumn('path')->removable();
```

The first parameter of the `multipleFile` method uses the relationship name `attachments`, and the method `pathColumn` is used to specify that the file upload path is stored in the `path` field of the association table.

## Storage path/file name

```php

// Modify the upload directory
$form->image('picture')->move('public/upload/image1/');

// Use a randomly generated file name (md5(uniqid()).extension)
$form->image('picture')->uniqueName();

// Custom file name
$form->image('picture')->name(function ($file) {
    return'test.'.$file->guessExtension();
});

```

## Upload configuration

Before using file upload, you must first complete the necessary configuration, configure local upload and cloud upload according to your situation

### Local Upload

First add storage configuration, `config/filesystems.php` add a `disk`:

```php

'disks' => [
    ...,

    'admin' => [
        'driver' =>'local',
        'root' => public_path('uploads'),
        'visibility' =>'public',
        'url' => env('APP_URL').'/uploads',
    ],
],

```

Set the upload path to `public/uploads` (public_path('uploads')).

Then select the uploaded `disk` and open `config/admin.php` to find:

```php

'upload' => [

    'disk' =>'admin',

    'directory' => [
        'image' =>'images',
        'file' =>'files',
    ]
],

```

Set `disk` to the `admin` added above, `directory.image` and `directory.file` to use `$form->image($column)` and `$form->file($column)` Upload directory for uploaded pictures and files.

### Cloud upload

If you need to upload to cloud storage, you need to install the adapter corresponding to `laravel storage`, take Qi Niu cloud storage as an example

First install [zgldh/qiniu-laravel-storage](https://github.com/zgldh/qiniu-laravel-storage)

Also configure the disk, add an item in `config/filesystems.php`:

```php
'disks' => [
    ...,
    'qiniu' => [
        'driver' =>'qiniu',
        'domains' => [
            'default' =>'xxxxx.com1.z0.glb.clouddn.com', //your domain name
            'https' =>'dn-yourdomain.qbox.me', //your HTTPS domain name
            'custom' =>'static.abc.com', //your custom domain name
         ],
        'access_key'=>'', //AccessKey
        'secret_key'=>'', //SecretKey
        'bucket' =>'', //Bucket name
        'notify_url'=>'', //Persistent processing callback address
        'url' =>'http://of8kfibjo.bkt.clouddn.com/', // fill in the file to access the root url
    ],
],

```

Then modify the upload configuration of `laravel-admin`, open `config/admin.php` and find:

```php

'upload' => [

    'disk' =>'qiniu',

    'directory' => [
        'image' =>'image',
        'file' =>'file',
    ],
],

```

`disk` select `qiniu` configured above.
