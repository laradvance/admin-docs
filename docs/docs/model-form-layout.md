# Form layout

> Since v1.7.6

![image](https://user-images.githubusercontent.com/9959927/62914561-5a229900-bdc3-11e9-8404-a595c7f589aa.png)

Similar to the left and right column layouts shown above, you can refer to the following code to achieve

```php
$form = new Form(new Document);

// The first column occupies 1/2 of the page width
$form->column(1/2, function ($form) {

    // Add a form item to this column
    
    $form->text('title', __('Title'))->rules('min:10');
    
    $form->textarea('desc', __('Desc'))->required();
    
    $form->select('uploader_id', __('Uploader'))
        ->options(User::all()->pluck('name', 'id'))
        ->rules('required');
    ;

    $form->file('path', __('Path'))->required();
});

// The second column occupies 1/2 of the page width to the right
$form->column(1/2, function ($form) {
    $form->number('view_count', __('View count'))->default(0);
    
    $form->number('download_count', __('Download count'))->default(0);
    
    $form->number('rate', __('Rate'))->default(0);
    
    $form->radio('privilege', __('Privilege'))
        ->options(Document::$privileges)
        ->stacked()
        ->default(1);

    $form->datetimeRange('created_at', 'updated_at');
});

return $form;

```

> The layout of the form is displayed using the grid layout system of bootstrap. The width ratio of the columns can be set to `1/2`, `1/3`, `1/4`, `1/6`.