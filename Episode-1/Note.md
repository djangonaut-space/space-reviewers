### 🚀 Space Reviewers 👾 - Episode 1 

PR: [#18468](https://github.com/django/django/pull/18468/)
ticket: [#25705](https://code.djangoproject.com/ticket/25705)

### The problem
the basic problem is related to string interpolation. The ticket wants to receive a query with:
* right quoting (rinchiuso tra apici)
* adaptation (convert the data according to their specific type: Python : None -> SQL: NULL )

In a few words, Django (now) simply converting the Python string directly can cause the previous problems.

### Documentation
[QuerySet API attribute](https://docs.djangoproject.com/en/dev/ref/models/querysets/#queryset-api)

### Other solutions (discarded)
There are different solution proposed:
* utlize `sql_with_params` (a previous ticket [#34636](https://code.djangoproject.com/ticket/34636) marked as wonfix) because there are still concerned, for example Mariusz said: *always uses the default database connection[..]is only for advanced users, who know the risks.* [comment20](https://code.djangoproject.com/ticket/25705#comment:20)
* Another concer is related with sql injection if the `sql.Query.__str__` perform the proper quoting invitng to use this function eve if it should not used for this purpose. [comment13](https://code.djangoproject.com/ticket/25705#comment:13)


