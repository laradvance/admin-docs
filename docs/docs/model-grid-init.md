# Grid initialization settings

> Since v1.6.10 [#3054](https://github.com/z-song/laravel-admin/pull/3054)

In the version `v1.6.10`, the initial setting function of the table is added to set the table globally.

For example, if you need to disable some operations in all tables, you can add the following code in `app/Admin/bootstrap.php`:

```php
use Encore\Admin\Grid;

Grid::init(function (Grid $grid) {

    $grid->disableActions();

    $grid->disablePagination();

    $grid->disableCreateButton();

    $grid->disableFilter();

    $grid->disableRowSelector();

    $grid->disableColumnSelector();

    $grid->disableTools();

    $grid->disableExport();

    $grid->actions(function (Grid\Displayers\Actions $actions) {
        $actions->disableView();
        $actions->disableEdit();
        $actions->disableDelete();
    });
});
```

This way you don't have to set it in the code of each controller.

If you want to enable settings in one of the tables, such as opening the display action column, call `$grid->disableActions(false);` on the corresponding instance.