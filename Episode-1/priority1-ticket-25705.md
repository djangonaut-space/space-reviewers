# 🚀 Space Reviewers 👾 - Episode 1 

## Details
Ticket: [#25705](https://code.djangoproject.com/ticket/25705)
PR: [#18468](https://github.com/django/django/pull/18468/)
Stream: TBD

## Problem

The ticket is looking for an ability to get a string version of the executed SQL of a
`QuerySet` that can be used directly in the database. The basic problem is related
to string interpolation.

The ticket wants to receive a stringified query with:

* Correct quoting (rinchiuso tra apici)
* Type adaptation (convert the data according to their specific type: Python : None -> SQL: NULL )

### Example

```python
from datetime import timedelta
from django.contrib.auth.models import User
from django.utils import timezone

users = User.objects.filter(date_joined__gt=timezone.now() - timedelta(days=365)).only('username')
print(users.query)

"""
SELECT "auth_user"."id",
       "auth_user"."username"
FROM "auth_user"
WHERE "auth_user"."date_joined" > 2023-11-16 03:59:38.146361
"""
```

The printed query can't be copy and pasted into a SQL shell because the datetime needs to be
converted to the databases expected type, such as `"date_joined" > '2023-11-16 03:59:38.146361'`


## Documentation
[QuerySet API attribute](https://docs.djangoproject.com/en/dev/ref/models/querysets/#queryset-api)

## Other solutions (discarded)
There are different solution proposed:
* utlize `sql_with_params` (a previous ticket [#34636](https://code.djangoproject.com/ticket/34636) marked as wonfix) because there are still concerned, for example Mariusz said: *always uses the default database connection[..]is only for advanced users, who know the risks.* [comment20](https://code.djangoproject.com/ticket/25705#comment:20)
* Another concer is related with sql injection if the `sql.Query.__str__` perform the proper quoting invitng to use this function eve if it should not used for this purpose. [comment13](https://code.djangoproject.com/ticket/25705#comment:13)


