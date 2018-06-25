# Conditional endings of the translation keys

Consider an example:

```django
{% if error == "max_file_size_violation" %}
    {% trans "error.max_file_size_violation" %}
{% endif %}
{% if error == "file_type_violation" %}
    {% trans "error.file_type_violation" %}
{% endif %}
```

A more generic way, although a little unsafe, to end a
translation key would be

```django
{% trans "error."|add:error %}
```

