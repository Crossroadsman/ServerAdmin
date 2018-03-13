Gunicorn (Green Unicorn)
========================

1: Background
-------------
[Gunicorn][link01] is an app server inspired by Ruby's Unicorn.

2: Installation
---------------
- Gunicorn is a Python application so we can install it using pip from inside our [virtual environment][link02]
  ```
  pip install gunicorn
  ```
- Test the installation by `cd`ing to the project's root directory and load Gunicorn (binding it to the project's WSGI file):
  ```
  gunicorn --bind 0.0.0.0:8000 <my_project>.wsgi
  ```
  This will connect the `wsgi.py` file in the `<my_project>` subdirectory to Gunicorn.








[link01]: http://gunicorn.org
[link02]: https://github.com/Crossroadsman/ServerAdmin/blob/master/PostgresWithDjango.md
