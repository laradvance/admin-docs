# Grid Header & Footer

After the `v1.6.10` version, two blocks of head and foot are added to the grid, and you can fill in the content you want.

```php
$grid->header(function ($query) {
    return 'header';
});

$grid->footer(function ($query) {
    return 'footer'; 
});
```

The parameter `$query` of the closure function is an instance of `\Illuminate\Database\Eloquent\Builder`, which can be used to execute some custom queries to get data. The following are examples of the use of two different scenarios.
## Header

For example, to insert a pie chart in the table header of the user data module to display the user gender ratio, you can follow the following method to achieve

First query the gender ratio data:
```php
$grid->header(function ($query) {

    $gender = $query->select(DB::raw('count(gender) as count, gender'))
        ->groupBy('gender')->get()->pluck('count', 'gender')->toArray();

    $doughnut = view('admin.chart.gender', compact('gender'));
    
    return new Box('Gender ratio', $doughnut);
});
```

Then use the chart extension [chartjs](https://github.com/laravel-admin-extensions/chartjs), to build the chart, the view file `resources/views/admin/chart/gender.blade.php` is as follows

```html
<canvas id="doughnut" width="200" height="200"></canvas>
<script>
$(function () {

    var config = {
        type: 'doughnut',
        data: {
            datasets: [{
                data: [
                    {{ $gender['m'] }},
                    {{ $gender['f'] }},
                    {{ $gender[''] }}
                ],
                backgroundColor: [
                    'rgb(54, 162, 235)',
                    'rgb(255, 99, 132)',
                    'rgb(255, 205, 86)'
                ]
            }],
            labels: [
                'Male',
                'Female',
                'Unknown'
            ]
        },
        options: {
            maintainAspectRatio: false
        }
    };

    var ctx = document.getElementById('doughnut').getContext('2d');
    new Chart(ctx, config);
});
</script>
```

The effect is as follows:

![WX20190310-195204](https://user-images.githubusercontent.com/1479100/54084635-1b993600-436e-11e9-97ab-4cb5fa5cff87.png)

## Footer

A more common scenario is to display statistical information at the foot of the data table. For example, to add revenue statistics to the footer of the order table, you can refer to the following code:

```php
$grid->footer(function ($query) {

    // Query the total amount of the order with the paid status
    $data = $query->where('status', 2)->sum('amount');

    return "<div style='padding: 10px;'>Total revenue ： $data</div>";
});
```

If you have a more complex foot that needs to be displayed, you can use the view object as you did in the head example.