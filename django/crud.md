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
    - `all()` to get a `QuerySet` object containing all the entries of the specified model:
      ```Python
      <var_name> = <Model_name>.objects.all()
      ```
    - `filter()` to get a `QuerySet` object containing the matching entities (in this example '*publish*' is a model property that is of type `DateTimeField()`, whose components can be queried, *author* is a User object with a *username* field):
      ```Python
      <var_name> = <Model_name>.objects.filter(publish__year=2016)
      
      <var_name> = <Model_name>.objects.filter(publish__year=2016, auther__username='admin')
      ```
    - `exclude()` to reduce a `QuerySet` by excluding items matching the specified criteria:
      ```Python
      <reduced_QuerySet> = <some_QuerySet>.exclude(title__startswith='why')
      ```
    - `order_by()` to specify the order of the returned `QuerySet`:
      ```Python
      <Model_name>.objects.order_by('-<some_variable>') # the - character reverses the sort order
      ```

- Update
   - Option 1: editing an in-memory variable:
     ```Python
     <var_name>.<property> = <new_value>
     <var_name>.save()
     ```
   - Option 2: editing a database entry directly:
     ```Python
     <Model_name>.objects.<TBD:set>
     ```

- Delete
    - Option 1: deleting an in-memory object:
      ```Python
      <var_name>.delete()
      ```

[link01]: https://docs.djangoproject.com/en/1.11/ref/exceptions/#objectdoesnotexist
[link02]: https://docs.djangoproject.com/en/1.11/ref/exceptions/#multipleobjectsreturned
