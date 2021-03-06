# rails-ajax-forms

> adding `remote: true` to Rails forms with wild abandon

## Playing with `form_tag` options

All of the following form examples are built using Rails'
[`form_tag`](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-form_tag).
I found the documentation a bit lacking, so I thought I would contribute my
own (more extensive) documentation here.

The idea is that these forms are being used to make
[Ajax](https://developer.mozilla.org/en/docs/AJAX) requests. To enable this
Ajax behavior, all the forms have `remote` set to `true`. If you want the
forms to make normal (non-Ajax) requests to the Rails app, just leave
`remote` alone because it defaults to `false`.

To give these forms some body, they each include a text field and submit
button, but these do not currently do anything interesting. This is
collapsed from all but the first example because the import part is the `<%=
form_tag ... %>` tag and its resulting `<form>` tag.

The source for all of these forms is available in
[`app/views/pages/simple_forms.html.erb`](https://github.com/jbranchaud/rails-ajax-forms/blob/master/app/views/pages/simple_forms.html.erb).
If you clone and serve the project locally, it an be accessed at
`/pages/simple_forms`.

This is a basic Ajax form that explicitly defines the URL to which the
request should be submitted.

```erb
<%= form_tag( '/pages/simple_forms', remote: true ) do %>
  <%= text_field_tag 'search_field', "" %>
  <%= submit_tag "Search", name: nil %>
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms" data-remote="true" method="post">
  <div style="margin:0;padding:0;display:inline">
    <input name="utf8" type="hidden" value="&#x2713;" />
  </div>
  <input id="search_field" name="search_field" type="text" value="" />
  <input type="submit" value="Search" />
</form>
```

This is a basic Ajax form with the Rails action specified rather than an
explicit URL. I am saying that I want the form request to be handled by that
action. The action's controller will be inferred as the controller for
this view. That controller plus the specified view will be resolved to the
URL which appears in the HTML.

```erb
<%= form_tag( { action: :simple_forms }, remote: true ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms" data-remote="true" method="post">
  ...
</form>
```

This is a basic Ajax form with both the controller and the action specified
in place of an explicit URL. By also declaring the controller, I have clear
indicated which action I intend. This also gives me the ability to specify
an action residing in a controller other than the current one. As in the
previous example, the controller and action specified will be resolved by
Rails to a URL that is used in the resulting HTML.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms }, remote: true ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms" data-remote="true" method="post">
  ...
</form>
```

You may have noticed in the previous two examples that the `action` and
`controller` options were specified within a separate hash. This hash is the
`url_for_options` argument and it contains the options related to the URL
that will be requested by this form. I will show in a few examples how that
will also give me the opportunity to specify the format I am requesting.

This form is equivalent to the previous one except that I specify the
request be sent with the `get` method rather than the `post` method.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms }, remote: true, method: :get ) do %>
  ...
<% end %>
</div>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms" data-remote="true" method="get">
  ...
</form>
```

If I am using the `get` method to request some data with the response, I may
want to request that data be in `json` format. I can request `json` data
using an explicit URL that ends in `.json`.

```erb
<%= form_tag( '/pages/simple_forms.json', remote: true, method: :get ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json" data-remote="true" method="get">
  ...
</form>
```

That seems a little dirty though. I would rather specify the `action` and
`controller` as I was doing before as well as the `json` format and let
Rails do the work of building the URL. I can do just that by jumping
back to the code I had before the previous example and then add the
`format` option to the `url_for_options` hash.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json" data-remote="true" method="get">
  ...
</form>
```

If I were a bit more old school, perhaps I would want to request some `xml`
data instead of `json` data. A small modification to the previous form,
specifying the format as `xml` rather than `json` will get me there.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :xml },
  remote: true, method: :get ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.xml" data-remote="true" method="get">
  ...
</form>
```

I am going to jump back to the `json` format for now. This next example
shows how I can add a `class` attribute to the form allowing me to style the
form or do weird things using jQuery. The `class` attribute is one of the
*options* and can be specified just like I specified the method. I can
optionally assign a string wrapped in quotes.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, class: :formtastic ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  class="formtastic" data-remote="true" method="get">
  ...
</form>
```

If I am going to add a couple class selectors to the `class` attribute, then
I need to wrap it in brackets making it a list. Alternatively, I can wrap it
all in quotes with each separated by spaces as I would do if I was writing
the actually markup by hand.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, class: [:formtastic, :responsively] ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  class="formtastic responsively" data-remote="true" method="get">
  ...
</form>
```

I can also add an `id` attribute if I'd like. It is even easier than the
`class` attribute because it is three characters less that you have to type.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, id: "single-id-form" ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  data-remote="true" id="single-id-form" method="get">
  ...
</form>
```

As for adding multiple `id` attributes, I will opt for approach that wraps
them in quotes. The other methods I've used above are also options and is a
matter of personal preference as well as consistency across the code base.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, id: "multi-id-form weird-form" ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  data-remote="true" id="multi-id-form weird-form" method="get">
  ...
</form>
```

I can also add other kinds of standard attributes to the form. For instance,
to make my intent clear, I can specify the
[`role`](http://www.w3.org/TR/role-attribute/) of this form to be
`form`. Sounds a little redundant, but why not?!

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, role: :form ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  data-remote="true" method="get" role="form">
  ...
</form>
```

Now all at once.

```erb
<%= form_tag( { controller: :pages, action: :simple_forms, format: :json },
  remote: true, method: :get, class: :formtastic, id: :form1337, role: :form ) do %>
  ...
<% end %>
```

```html
<form accept-charset="UTF-8" action="/pages/simple_forms.json"
  class="formtastic" data-remote="true" id="form1337" method="get" role="form">
  ...
</form>
```
