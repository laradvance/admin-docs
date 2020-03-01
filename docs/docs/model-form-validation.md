# Form validation

`model-form` uses `Laravel`'s validation rules to verify the data submitted by the form:

```php
$form->text('title')->rules('required|min:3');

// Complex validation rules can be implemented in the callback
$form->text('title')->rules(function ($form) {

    // If it is not an edit state, add field unique verification
    if (!$id = $form->model()->id) {
        return 'unique:users,email_address';
    }

});

```

You can also customize the error message for the validation rule:

```php
$form->text('code')->rules('required|regex:/^\d+$/|min:10', [
    'regex' => 'code must be numbers',
    'min'   => 'code can not be less than 10 characters',
]);
```

If you want to allow the field to be empty, first in the database table to face the field set to `NULL`, and then

```php
$form->text('title')->rules('nullable');
```

Please refer to the more rules [Validation](https://laravel.com/docs/5.5/validation).

## Create page rules

Only effective when creating a form submission

```php
$form->text('title')->creationRules('required|min:3');
```

## Update page rules

Only effective when the update form is submitted

```php
$form->text('title')->updateRules('required|min:3');
```

## Database unique check

A more common scenario is to submit a form to check if the data already exists. You can use the following method:

```php
$form->text('username')
     ->creationRules(['required', "unique:user_table"])
     ->updateRules(['required', "unique:user_table,username,{{id}}"]);
```