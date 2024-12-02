# 🚀 Space Reviewers 👾 - Episode 1 

## Details

- Ticket: [#25705](https://code.djangoproject.com/ticket/25705)
- PR: [#18468](https://github.com/django/django/pull/18468/)
- Stream: [YouTube](https://www.youtube.com/live/m4o7cWFyIds)

## Problem

The ticket is looking for an ability to get a string version of the executed SQL of a
`QuerySet` that can be used directly in the database. The basic problem is related
to string interpolation.

The ticket wants to receive a stringified query with:

* Correct quoting (properly enclosed in quotes)
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

## Summarized issue discussion

1. [The original request](https://code.djangoproject.com/ticket/25705#comment:5) was to be able to do the following:
   ```
   sql = str(MyModel.objects.all().query)
   raw_qs = MyModel.objects.raw(sql)
   ```
2. [Alex determined](https://code.djangoproject.com/ticket/25705#comment:12) that it would be
   impossible to do this for all databases. Oracle and SQLIte don't support
   a mogrify function like PostgreSQL and MySQL.
   - [Mogrify was later found](https://code.djangoproject.com/ticket/25705#comment:17) to not
    be a valid option anyway.
3. [A concern was raised](https://code.djangoproject.com/ticket/25705#comment:16)
   that people are attempting to convert a `QuerySet` to a SQL string to
   be executed on the database. This meant that steps would need to be taken to prevent that
   anti-pattern.
4. There's a proposal to [support a `QuerySet.executed_query`](https://code.djangoproject.com/ticket/25705#comment:16)
   or `QuerySet.last_query` by Simon.
   - [Alex agreed](https://code.djangoproject.com/ticket/25705#comment:17)
   - There's some uncertainity around [how to handle a `executed_query`](https://code.djangoproject.com/ticket/25705#comment:18)
    when a `QuerySet` hasn't been evaluated yet.
5. [Alex drafts a PR with a solution](https://code.djangoproject.com/ticket/25705#comment:22)
   along the following lines, any further discussion is on the PR.
   > ... the flow is something like
   > QuerySet when ​[evaluating](https://github.com/django/django/blob/d5bebc1c26d4c0ec9eaa057aefc5b38649c0ba3b/django/db/models/query.py#L1909)
   > calls the ​[IterableClass](https://github.com/django/django/blob/d5bebc1c26d4c0ec9eaa057aefc5b38649c0ba3b/django/db/models/query.py#L82)
   > which then [​executes the query](https://github.com/django/django/blob/d5bebc1c26d4c0ec9eaa057aefc5b38649c0ba3b/django/db/models/query.py#L91)
   > which then gets the [​connection cursor](https://github.com/django/django/blob/d5bebc1c26d4c0ec9eaa057aefc5b38649c0ba3b/django/db/models/sql/compiler.py#L1585),
   > which is wrapped in the previous mentioned one that can log the query.
   > 
   > So the a way would be to make all 4 `SqlCompiler.execute_sql` return the result and the SQL query. Then you have to adapt all 6 `Iterable.__iter__` to also return the query. Then you can store it in the Queryset. Since these methods are part of the public API, this would be a breaking change in both methods. Also I'm not 100% sure how to get the query in the wrapper since the `Cursor.execut`e is wrapped in a context manager which is the one logging the query. 

## Appendix

### Documentation

[QuerySet API attribute](https://docs.djangoproject.com/en/dev/ref/models/querysets/#queryset-api)

### Other topics

Two other important topics were discussed in the Trac thread:
* Support `QuerySet.query.sql_with_params()` (a previous ticket [#34636](https://code.djangoproject.com/ticket/34636) marked as wonfix) because there are still concerns, for example Mariusz said: *always uses the default database connection [..] is only for advanced users, who know the risks.* [comment20](https://code.djangoproject.com/ticket/25705#comment:20)
  * [This was re-opened](https://code.djangoproject.com/ticket/25705#comment:21) with a
    technical proposal of how to avoid users from doing `Model.objects.raw(str(queryset.query))`.
* Another concern is related with sql injection if the `sql.Query.__str__` perform improper quoting. Once this anti-pattern is supported, people will use it and it'll need to be permanently supported. [comment13](https://code.djangoproject.com/ticket/25705#comment:13)


### Topics discussed in the chat: 

1. **Logging QuerySet:**
Logging every query executed by a QuerySet was discussed as a potential feature. However, there are some considerations to note:

>*Alex:*
> 
>I can see the logging every query the queryset make being a possibility.
>Some notes I'd have are:
>- If the queryset is evaluated multiple times, does each time get added to the list
>- The implementation gets complicated, since in the case of 5 prefetch you have to pick the last 5 executed queries.

2. **Naming: `query` or `queries`:**
The choice of names for properties like `executed_query` is important to ensure they align with what users expect, especially when dealing with multiple queries.

>*Sarah:*
> 
>So with the prefetch thought, let's say we want these queries later. Would `executed_query` still make sense? Or base_query or would we need to deprecate it for a list? I think strategic names should make this clear
> [..]
> 
>*Alex:*
> 
>I like the executed_base_query idea in case of a future executed_queries


3. **Handling None QuerySets:**
The `none` case needs consideration. This point was a key to our review:

>*Sage:*
> 
>What happens with MyModel.objects.none()?
>
> [..]
> 
>*Alex:*
> 
>Yeah, it's resetted on an evaluation that goes to database, so it doesn't handle the none() case. 
>I didn't think of none() at all.
