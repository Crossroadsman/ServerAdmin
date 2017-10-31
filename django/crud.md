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
  ```Python
  <var_name> = <Model_name>.objects.get(<property>='<property_value>')
  ```
