# Data form

> Since v1.7.0

`Data form` is used to build forms and process submission data, which can be easily processed to process data that is not related to the Eloquent model.

## Basic usage

Generate the form class file with the command `admin:form`:

```shell
php artisan admin:form Setting --title=Website settings`
```

Will generate the form file `app/Admin/Forms/Setting.php`

```php
<?php

namespace App\Admin\Forms;

use Encore\Admin\Widgets\Form;
use Illuminate\Http\Request;

class Setting extends Form
{
    /**
     * The form title.
     *
     * @var string
     */
    public $title = 'Settings';

    /**
     * Handle the form request.
     *
     * @param Request $request
     *
     * @return \Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request)
    {
        //dump($request->all());

        admin_success('Processed successfully.');

        return back();
    }

    /**
     * Build a form here.
     */
    public function form()
    {
        $this->text('name')->rules('required');
        $this->email('email')->rules('email');
        $this->datetime('created_at');
    }

    /**
     * The data of the form.
     *
     * @return array $data
     */
    public function data()
    {
        return [
            'name'       => 'John Doe',
            'email'      => 'John.Doe@gmail.com',
            'created_at' => now(),
        ];
    }
}

```

In the form class above, `$title` is used to set the title of the form, and the form item is built in the `form` method. The method is consistent with `model-form`, and the `data` method is used to set the form item. Default data

Then put the above form into your page as follows:

```php
<?php

use App\Admin\Forms\Setting;
use App\Http\Controllers\Controller;
use Encore\Admin\Layout\Content;

class UserController extends Controller
{
    public function setting(Content $content)
    {
        return $content
            ->title('Website setting')
            ->body(new Setting());
    }
}
```

After filling in the data submission form on the page, the request will go to the `handle` method, where you can add your data processing logic. After the processing is complete, return a response object to the front end:

```php
    public function handle(Request $request)
    {
        // Get data from the $request object to process...

        // Add a success prompt
        admin_success('数据处理成功.');

        // or an error message
        admin_success('数据处理成功失败.');

        // Go back to the original form page after processing is complete, or jump to another page by returning `redirect()` method
        return back();
    }
```

## Tab form

`tab form` Organize multiple forms by using the line selection card, click the tab to jump to the corresponding form page

How to use

```php
use App\Admin\Forms\Settings;
use App\Http\Controllers\Controller;
use Encore\Admin\Layout\Content;
use Encore\Admin\Widgets\Tab;

class FormController extends Controller
{
    public function settings(Content $content)
    {
        $forms = [
            'basic'    => Settings\Basic::class,
            'site'     => Settings\Site::class,
            'upload'   => Settings\Upload::class,
            'database' => Settings\Database::class,
            'develop'  => Settings\Develop::class,
        ];

        return $content
            ->title('系统设置')
            ->body(Tab::forms($forms));
    }
}
```

Where `$forms` is an array of `data form` types, the page effect is as follows:

![WX20190608-143841](https://user-images.githubusercontent.com/1479100/59143217-23af5300-89fb-11e9-85d3-88e3451474d5.png)

## Step form

If you want to divide a form into multiple steps, you can use the `step form`:

```php
<?php

use App\Admin\Forms\Steps;
use App\Http\Controllers\Controller;
use Encore\Admin\Layout\Content;
use Encore\Admin\Widgets\MultipleSteps;

class FormController extends Controller
{
    public function register(Content $content)
    {
        $steps = [
            'info'     => Steps\Info::class,
            'profile'  => Steps\Profile::class,
            'password' => Steps\Password::class,
        ];

        return $content
            ->title('Register')
            ->body(MultipleSteps::make($steps));
    }
}
```

The code for the first step table with class `app/Admin/Forms/Steps/Info.php` is as follows:

```php
<?php

namespace App\Admin\Forms\Steps;

use Encore\Admin\Widgets\StepForm;
use Illuminate\Http\Request;

class Info extends StepForm
{
    /**
     * The form title.
     *
     * @var string
     */
    public $title = 'Basic info';

    /**
     * Handle the form request.
     *
     * @param Request $request
     *
     * @return \Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request)
    {
        return $this->next($request->all());
    }

    /**
     * Build a form here.
     */
    public function form()
    {
        $this->text('username');
        $this->email('email');
    }
}
```

> Note that the inheritance of the step form is the `Encore\Admin\Widgets\StepForm` class.

In the `handle` method, there are some ways to help deal with various situations.

Use the `next` method to save the current step data to the session and jump to the next step:

```php
return $this->next($data = []);
```

Or data processing fails, go back to the previous step:

```php
return $this->prev();
```

Clear the data in the session after processing the data in the last step:

```php
return $this->clear();
```

Get all previous data through the `all` method

```php
return $this->all();
```