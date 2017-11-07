URL Patterns
============

`url()` calls in the `urlpatterns` list take the following form:

```Python
url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive, {'foo': 'bar'}, name='articles')
```

The first argument:

```Python
r'^articles/(?P<year>[0-9]{4})/$'
```

is the regex to be matched. Note that if there are any captures groups they will be forwarded to the view as either positional 
arguments (if the capture groups are unnamed) or as named arguments (if they capture groups are named) (note, capture group naming
is all or nothing).

The second argument:

```Python
views.year_archive
```

is the callback method. Note that this can be a direct path to a view (as in this example), or an include of another url list, or
a class-based view.

The third (optional) argument:

```Python
{'foo': 'bar'}
```

is a dictionary that will be passed to the destination method as arguments after the capture group arguments (if any).

The fourth (optional) argument:

```Python
name='articles'
```

is the name of the pattern which is used for [url reversing][link01].



[link01]: https://docs.djangoproject.com/en/1.11/ref/urlresolvers/#django.urls.reverse
