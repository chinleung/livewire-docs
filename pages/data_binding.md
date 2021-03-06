# Data Binding
If you've used front-end frameworks like Angular, React, or Vue, you are already familiar with this concept. However, if you are new to this concept, allow me to demonstrate.

Consider the following Livewire component:

<div title="Component"><div title="Component__class">

MyNameIs
```php
class MyNameIs extends LivewireComponent
{
    public $name;

    public function render()
    {
        return view('livewire.my-name-is');
    }
}
```
</div>
<div title="Component__view">

my-name-is.blade.php
```html
<div>
    What's your name?
    <input type="text" wire:model="name">

    My name is chica-chica {{ $name }}
</div>
```
</div>
</div>

When the user types "Slim Shady" into the text field, the value of the `$name` property will automatically update. Livewire knows to keep track of the provided name because of the `wire:model` directive.

Internally, Livewire listens for "input" events on the element and updates the class property with the element's value. Therefore, you can apply `wire:model` to any element that emits `input` events.

Common elements to use `wire:model` on include:

Element Tag |
--- |
`<input type="input">` |
`<input type="radio">` |
`<input type="checkbox">` |
`<select>` |
`<textarea>` |

<div title="Warning"><div title="Warning__content">

Be careful using `wire:model` on `<input>` elements, it is usually better to use `wire:model.lazy` instead. See below for more info.
</div></div>

## Lazily Updating

By default, Livewire sends a request to server after every "input" event. This is usually fine for things like `<select>` elements that don't update frequently, however, this is often unnescessary for text fields that update as the user types.

In those cases, use the `lazy` directive modifier to listen for the native "change" event.

<div title="Component"><div title="Component__class">

MyNameIs
```php
class MyNameIs extends LivewireComponent
{
    public $name;

    public function render()
    {
        return view('livewire.my-name-is');
    }
}
```
</div>
<div title="Component__view">

my-name-is.blade.php
```html
<div>
    What's your name?
    <input type="text" wire:model.lazy="name">

    My name is chica-chica {{ $name }}
</div>
```
</div>
</div>

Now, the `$todo` property will only be updated when the user clicks away from the input field.
