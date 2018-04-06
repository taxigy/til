# Expose a model into base.html

According to Django template system, when a view renders a template and passes
context variables into it, and this template
[extends](https://tutorial.djangogirls.org/en/template_extending/), it's
considered harmful to access those context variables from the base template
(commonly referred to as "base.html").

Consider an example view, `BananaView`:

```python
from django.shortcuts import render

from .models import BananaModel


class BananaView(View):
  def get(self, request):
    return render(request, 'bananas.html', {'bananas': BananaModel.objects.all()})
```

Then the template bananas.html:

```html
{% extends "base.html" %}
{% block content %}
  {% for b in banana %}
    {{ b.name }}
  {% endfor %}
{% endblock %}
```

Now, let's assume we need to display that same list of bananas in the navigation
bar declared in base.html:

```html
{% load static %}<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Bananas</title>
  </head>
  <body>
    <nav class="nav">
      <ul class="nav__dropdown">
        {% for b in bananas %}
        <li class="nav__dropdown-item">{{ b.name }}</li>
        {% endfor %}
      </ul>
    </nav>
    <main class="content">
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

It won't work with any other view that doesn't expose `bananas` context
variable.

To make it work, it's possible to use [simple
tags](https://docs.djangoproject.com/en/2.0/howto/custom-template-tags/#custom-template-tags-and-filters).
For example, declare one in file custom_tags.py:

```python
from django import template

from banana.models import BananaModel

register = template.Library()


@register.simple_tag
def get_all_bananas():
  return BananaModel.objects.all()
```

This way, we have a global custom template tag that exposes the whole model (be
careful with sensitive data here), so we can rewrite our base.html as

```html
{% load static custom_tags %}<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Bananas</title>
  </head>
  <body>
    <nav class="nav">
      <ul class="nav__dropdown">
        {% get_all_bananas as all_bananas %}
        {% for b in all_bananas %}
        <li class="nav__dropdown-item">{{ b.name }}</li>
        {% endfor %}
      </ul>
    </nav>
    <main class="content">
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

This way it's guaranteed that `.nav__dropdown-item` elements will always
correctly represent all bananas without relying on child template's context
variables.
