# Rendering Components

There are two ways to render Livewire components:

* A) Include them in existing blade views.
* B) Render them as an entire page in your app.

## A) Include Inside Blade View

You can include a Livewire component in an existing Blade view with the `@livewire` directive.

Let's assume we have a route like `Route::get('/home', 'HomeController@show')`, and `HomeController` returns a view named `home.blade.php`. We can include a component called `counter` like so:

```php
@extends('layouts.app')

@section('content')

    @livewire('counter')

@endsection
```

### Initial Parameters

Additionally, you can pass data into a component by passing additional parameters into the `@livewire` directive. For example, let's say we have an `ShowContact` Livewire component that needs to know which contact to show. Here's how you would pass in the contact id.

```php
@livewire('show-contact', $contactId)
```

```php
class ShowContact extends LivewireComponent
{
    public $name;
    public $email;

    public function mount($id)
    {
        $contact = User::find($id);

        $this->name = $contact->name;
        $this->email = $contact->email;
    }

    ...
}
```

You can pass multiple parameters to the `mount()` hook and receive them like so:

```php
@livewire('show-contact', $contactId, $profilePhotoUrl)
```

## B) Render Component As Entire Page

If you find yourself writing controllers and views that only return a Livewire component, you might want to use Livewire's routing helpers to cut out the extra boilerplate code. Take a look at the following example:

*Before*
```php
// Route
Route::get('/home', 'HomeController@show');

// Controller
class HomeController extends Controller
{
    public function show()
    {
        return view('home');
    }
}

// View
@extends('layouts.app')

@section('content')
    @livewire('counter')
@endsection
```

**After**
```php
// Route
Route::livewire('/home', 'counter');
```

Note: for this feature to work, Livewire assumes you have a layout stored in `resources/views/layouts/app.blade.php` that yields a "content" section (`@yield('content')`)

### Custom Layout File
If you use a different layout file or section name, you can configure these in the standard way you configure laravel routes:

```php
// Customizing layout
Route::livewire('/home', 'counter')
    ->layout('layouts.base');

// Customizing section (@yield('body'))
Route::livewire('/home', 'counter')
    ->section('body');
```

You can also configure these settings for an entire route group using the group option array syntax:

```php
Route::group(['layout' => 'layouts.base', 'section' => 'body'], function () {
    //
});
```

Or the fluent alternative:
```php
Route::layout('layouts.base')->section('body')->group(function () {
    //
});
```

### Route Parameters

Often you need to access route parameters inside your controller methods. Because we are no longer using controllers, Livewire attempts to mimick this behavior through it's `mount` lifecycle hook. For example:

**web.php**
```php
Route::livewire('/contact/{id}', 'show-contact');
```

**App\Http\Livewire\ShowContact.php**
```php
class ShowContact extends LivewireComponent
{
    public $name;
    public $email;

    public function mount($id)
    {
        $contact = User::find($id);

        $this->name = $contact->name;
        $this->email = $contact->email;
    }

    ...
}
```

As you can see, the `mount` method in a Livewire component is acting like a controller method would as far as it's parameters go. If you visit `/contact/123`, the `$id` variable passed into the `mount` method will contain the value `123`.

### Route Model Binding

Like you would expect, Livewire components implement all functionality you're used to in your controllers including route model binding. For example:

**web.php**
```php
Route::livewire('/contact/{user}', 'show-contact');
```

**App\Http\Livewire\ShowContact.php**
```php
class ShowContact extends LivewireComponent
{
    public $contact;

    public function mount(User $user)
    {
        $this->contact = $user;
    }
}
```

Now, after visiting `/contact/123`, the value passed into `mount` will be an instance of the `User` model with id `123`.
