# 🚀 Space Reviewers 👾 - Episode 3

## Details

- Date: [2025-01-12 4pm UTC](https://time.is/compare/1600_12_January_2025_UTC)
- Ticket: [#26739](https://code.djangoproject.com/ticket/26739)
- PR: [#16890](https://github.com/django/django/pull/16890)
- Stream: [https://youtube.com/live/Z_OhCNd5RgM](https://youtube.com/live/Z_OhCNd5RgM)

## Problem

The ticket is about improving the default migration plan for removing
a field that is not nullable and has no default set. When a field does
not allow `NULL` and has no `default` set, and the field is removed
using `RemoveField`, the backward operation errors if the table
has at least one row, because the field will be created with no value.

## Summarized issue discussion

1. [Florian pointed out](https://code.djangoproject.com/ticket/26739#comment:1) that:
   > The autodetector should be updated to ask for a new default, tests and docs missing.

   Meaning that when the migration is being created, the user should be prompted to
   specify a default, ignore the problem, or exit before creating the migration. This
   is similar to creating a new field that can't be `NULL`, but in reverse.
3. [Simon suggested](https://code.djangoproject.com/ticket/26739#comment:2) this could
   "rely on the transient default of `AddField (preserve_default=False)`"

## Appendix

### Documentation

- [RemoveField reference docs](https://docs.djangoproject.com/en/5.1/ref/migration-operations/#removefield)
- [AddField reference docs](https://docs.djangoproject.com/en/5.1/ref/migration-operations/#addfield)
