# inplace-editing
A simple, modular, javascript package for in-place editing. Written in vanilla Javascript.

This is currently a work in progress and probably should be used carefully.

Use this by including `editor.js` with whichever method you like best.

Initialize the package by calling `Manager.init()`. This scans the page for `ip-field` tags and attaches on-click event listeners. (I'll probably end up renaming `Manager` to something more descriptive.)

```html
<script>
    document.addEventListener('DOMContentLoaded', Manager.init);
</script>
```

## How to use it

You use it by wrapping whatever text you want to be editable with `<ip-field>` tags.

###Here are some examples:

#### Text

```html
<ip-field type="text" name="email" post-url="/user/update" if-empty="Enter your email...">Enter your email...</ip-field>
```

#### Select form
```html
<ip-field type="select" name="prefix" post-url="/user/update" options="Mr.,Ms.,Mrs.,Dr.">Mr.</ip-field>
```

You can also supply JSON to the options parameter of select fields. JSON should be an array of objects with `value` and `display` keys.

```html
<ip-field type="select" name="subscriptionPlan" post-url="/user/update" options='[{"value": "500", "display": "Bronze"},{"value": "1000", "display": "Silver"},{"value": "1500", "display": "Gold"}]'>Bronze</ip-field>
```

#### Checkbox
```html
<ip-field type="checkbox" ip-class="testclass" ip-id="testid" name="status" post-url="/post" ip-false="Disabled" ip-true="Enabled" ip-toggle="Enabled">Enabled</ip-field>
```
* `ip-false` defines the text to display if the checkbox *is not* checked.
* `ip-true` defines the text to display if the checkbox *is* checked.
* `ip-toggle` defines the text to display when the checkbox is shown.

### Parameters

The following parameters work for any of the ip-fields:
* `ip-class` defines the classes to be set on the `input` element that is swapped in
* `ip-id` defines the id to be set on the `input` element that is swapped in

## Groups
Where I think this starts to get a bit more flexible and powerful is that you can group inputs. When an input in a group is clicked, it toggles a switch from text to input on all of those inputs. When it is saved, it toggles all the inputs in the group back to text and sends the data off as JSON in one request.

### Example
Here's an example of what I'm talking about, but with addresses.

```html
<ip-group post-url="/post">
    <ip-field type="text" name="street1">101 S. Congress</ip-field><br />
    <ip-field type="text" name="street2">Ste. 109</ip-field><br />
    <ip-field type="text" name="city">Austin</ip-field>,
    <ip-field type="text" name="state">TX</ip-field>
    <ip-field type="text" name="zipcode">78701</ip-field>
</ip-group>
```

If any of the fields in the group are clicked, all the fields get toggled together. In addition, when the save button (or the enter key) is pressed, the data gets sent to the `post-url` as one JSON request. I find it pretty useful.

Groups also have their own parameters that affect all the fields they encompass.

* `post-url` is the URL that the data is posted to. You techinically don't have to include this, but I don't know why you'd be using this if you weren't posting the data.
* `send-with` accepts JSON that gets added to the JSON request that is sent. This is useful if you need to attach a CSRF token or an entity ID.

## Configuration

You can also edit the main configuration of the `Manager` class that affects it's behavior.

Right now, the only thing that's supported is the `sendWith` parameter that gets added to every JSON request that is sent out for that page. This is really useful for things like CSRF requets, or method spoofing, as shown in the example below:

```html
<script>
csrf = document.querySelector('meta[name="csrf_token"]').getAttribute('value');
Manager.setConfig({
    sendWith: {
        "csrf": csrf,
        "method": "_put",
    }
});
</script>
```

You'll want to call the `Manager.setConfig()` function before you initialize the Manager.

## Contributing

I created this out of my own necessity and so I've really only been extending it/changing it as I came up with more use requiremenets. That being said, it's very opinionated and isn't really ready for production. If you find use for it, or want to contribute and help decide what this will continue to look like in the future, feature/pull requests are welcome.
