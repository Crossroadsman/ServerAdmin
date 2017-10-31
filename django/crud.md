CRUD
====

Shell setup
-----------
- Launch a shell: `./manage.py shell`
- Import project files you want to interact with:
    - admin.users:
      ```Python
      from django.contrib.auth.models import User
      ```
    - app's models:
      ```Python
      from <app_name>.models import <model_name>
      ```
        

The CRUD Operations
-------------------

- Create
    - Option 1: create (and write to the database) and return an object
      ```Python
      <Model_name>.objects.create(<property1>='<p1_value>', <property2>='<p2_value>', <etc>)
      ```
    - Option 2: create then separately write to the database (instantiation returns the object but persisting does not)
      ```Python
      <var_name> = <Model_name>(<property1>='<p1_value>', <property2>='<p2_value>', <etc>)
      <var_name>.save()
      ```

- Read
    - `get()` for a single instance (raises an exception if [0][link01] or [>1][link02] objects match the query)
       ```Python
       <var_name> = <Model_name>.objects.get(<property>='<property_value>')
       ```

- Update
   - Option 1: editing an in-memory variable:
     ```Python
     <var_name>.<property> = <new_value>
     <var_name>.save()
     ```
   - Option 2: editing a database entry directly:



[link01]: https://docs.djangoproject.com/en/1.11/ref/exceptions/#objectdoesnotexist
[link02]: https://docs.djangoproject.com/en/1.11/ref/exceptions/#multipleobjectsreturned
