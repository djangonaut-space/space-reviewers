---
title: PR review deep dive into Ticket 36352
date: 2025-05-31
---

# 🚀 Space Reviewers 👾 - Episode 5

## Details

This episode @ontowhee dives deep into her process for doing code reviews for Django PRs.

- Ticket: [#36352](https://code.djangoproject.com/ticket/36352)
- PR: [#19478](https://github.com/django/django/pull/19478)
- Video: [https://youtu.be/mPndbdezvJw](https://youtu.be/mPndbdezvJw)

## Problem

The ticket is about not being able to reference an annotated field when having multiple chains of `values()`. It is currently raising a FieldError, and the error suggests using another `annotate()` to promote the field.

## Summarized issue discussion

1. [Sarah bisected](https://code.djangoproject.com/ticket/36352#comment:9) the issue to [cb13792](https://code.djangoproject.com/changeset/cb13792938f2c887134eb6b5164d89f8d8f9f1bd/), and also provided simplified code to reproduce the issue.

2. [Simon suggested](https://code.djangoproject.com/ticket/36352#comment:10) a better error message could be raised, but also mentions that significant changes were made since 4.2 to prevent having ambiguity between existing field names and annotations.

3. [Sarah agrees](https://code.djangoproject.com/ticket/36352#comment:11) this could use a better error message.

## PR Changes

query.py
1. On [line 2522](https://github.com/django/django/pull/19478/files#diff-fd2300283d1546e36141373b0621f142ed871e3e8856e07efe5a22ecc38ad620R2522), a guard is added to only raise the FieldError if `f` (the field) is not in `annotation_names`.

2. On [lines 2527-2528](https://github.com/django/django/pull/19478/files#diff-fd2300283d1546e36141373b0621f142ed871e3e8856e07efe5a22ecc38ad620R2527-R2528), append `f` to `annotation_names`, and include f in `selected`.

3. On [lines 2538-2539](https://github.com/django/django/pull/19478/files#diff-fd2300283d1546e36141373b0621f142ed871e3e8856e07efe5a22ecc38ad620R2538-R2539), include the fields from `self.annotation_select_mask` in `annotation_names`.

test.py
1. Reproduction code from discussion is added into the test, and the test fails on main branch with the "Cannot select 'author_id' alias" as expected and passes with the patch.

   Note: Book.authors is a M2M field.

2. The simplified reproduction code is also added, and it also fails on the main branch as expected and passes with the patch.

## Questions

1. What is `f`?
	- A field that is being resolved.

2. What is `selected`?
	- Fields that are added to the select list.

3. What is `annotation_select`?
	- A [property of Query](https://github.com/django/django/blob/1a744343999c9646912cee76ba0a2fa6ef5e6240/django/db/models/sql/query.py#L2567), returns the annotations that should be included in the select list.

4. What is `annotation_select_mask`?
	- Annotations that should not be included in select list.
	- **Question:** When would an annotation be masked? Does this happen, for example, when the annotation is not included in values()?
	- [Comment 5](https://code.djangoproject.com/ticket/33975#comment:5) from ticket 33975 has some context about annotation select mask. Sounds like it helps determine which columns to include/exclude in the select list when annotate() and alias() are used together.

5. What is `annotation_names`?
	- A list of field names that were created via annotate(). Contains both masked and non-masked annotations.

6. `annotate()` va. `alias()`
	- [annotate()](https://docs.djangoproject.com/en/5.2/ref/models/querysets/#annotate) - augments the results of the QuerySet with additional expressions
	- [alias()](https://docs.djangoproject.com/en/5.2/ref/models/querysets/#alias) - creates references to expressions that are used in parts of the query other than the select list, such as order by, group by, filter, subqueries, etc. Use alias() if you do not want the QuerySet results to return the expression, but you want to use the expression in the query.

## Debugging Commands

```bash
# Run django-docker-box container with debugger port exposed and drop into bash shell.
docker compose run -p 5678:5678 --entrypoint /bin/bash postgresql

# On the IDE (vscode), set up the debugger to listen on port 5678.
# Inside the django-docker-box container, run the test with debugpy.
debugpy --wait-for-client --listen 0.0.0.0:5678 ./runtests.py annotations.tests.NonAggregateAnnotationTestCase.test_chained_values_annotation_fielderror
```

## Review
1. **Question:** Is the desired solution for the ticket to have a better error message? Or is it to allow the annotation to be referenced without raising the FieldError?

2. On lines 2527-2528, logic is added to append `f` to `annotation_names` and set selected[f] = f.

	**Question:** Would there be a case where `f` is NOT an annotation, yet this logic is adding it to `annotation_names`?

3. While the tests pass, the changes in lines 2522-2528 are not being covered by the tests. Does it need another test case? Are these lines being covered by another test?

4. On tests.py line 1243, could it use qs.count() instead of len()? Is checking the length sufficient?

## Resources used

- [django-docker-box](https://github.com/django/django-docker-box) for testing
- [Python Debugger Extension for VSCode](https://code.visualstudio.com/docs/python/debugging)
