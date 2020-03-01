# Form Initialization Settings

> Since v1.6.10 [#3054](https://github.com/z-song/laravel-admin/pull/3054)

In the version `v1.6.10`, the initial setting function of the form has been added to set the form globally.

For example, if you need to disable some operations in all forms, you can add the following code in `app/Admin/bootstrap.php`:

```php
Form::init(function (Form $form) {

    $form->disableEditingCheck();

    $form->disableCreatingCheck();

    $form->disableViewCheck();

    $form->tools(function (Form\Tools $tools) {
        $tools->disableDelete();
        $tools->disableView();
        $tools->disableList();
    });
});
```

If you want to open the settings in one of the forms, for example, open the checkbox that displays 'Continue Editing', call `$form->disableEditingCheck(false)` on the corresponding instance;