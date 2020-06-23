# Language localization

## Enable/Disable Multi Language

Modify extension config In `config/admin.php`

```php
    'extensions' => [
        'multi-language' => [
            //enable or disable multi language
            'enable' => true,

            // the value is used to show
            'languages' => [
                'en' => 'English',
                'id' => 'Indonesia',
            ],
            // default locale
            'default' => 'en',

            // if or not show multi-language login page, optional, default is true
            'show-login-page' => true,

            // if or not show multi-language navbar, optional, default is true
            'show-navbar' => true,

            // the cookie name for the multi-language var, optional, default is 'locale'
            'cookie-name' => 'locale'
        ],
    ],
```

## Supported language

To find out which languages are supported, look at the `resources/lang` folder.

## Controller field label

After running `admin:make` to create the controller, the labels of tables and forms use `__('Column name')` by default, as follows:

```php
    $grid->column('id', __('ID'));
    $grid->column('name', __('Name'));
    $grid->column('email', __('Email'));
    $grid->column('created_at', __('Created at'));
    $grid->column('updated_at', __('Updated at'));
```

You can directly modify the second parameter to the change field label, but the best way is to set the string translation, go to the resources/lang directory to create a new translation json file. For example `resources/lang/id.json`, and add the corresponding translation:

```json
{
  "ID": "ID",
  "Created at": "Dibuat pada",
  "Updated at": "Diperbaharui pada",
  "name": "Nama",
  "email": "Surel"
}
```

In this case, other controllers can also reuse the translation here.
