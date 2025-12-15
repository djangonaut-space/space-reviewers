# 🚀 Space Reviewers 👾 - Episode 8

## Details

- Ticket: [#35462](https://code.djangoproject.com/ticket/35462)
- PR: [#19406](https://github.com/django/django/pull/19406)
- Stream: YouTube

## Problem

Django’s `ArrayAgg` is limited to PostgreSQL, making equivalent aggregation functionality unavailable on other database backends. While databases such as MariaDB do not support native array types, they do provide JSON-based aggregation functions like `JSON_ARRAYAGG`, which are implemented across multiple database systems.

This limitation highlights the lack of a general-purpose, JSON-based aggregation function in the Django ORM that can be used consistently across all supported database backends.

## Summarized issue discussion

- The ticket started from a MariaDB-focused request: adding `ArrayAgg`-like functionality using MariaDB’s `JSON_ARRAYAGG`.
- [Simon clarified](https://code.djangoproject.com/ticket/35462#comment:3) that Django’s existing `ArrayAgg` is not equivalent on MariaDB because it aggregates into PostgreSQL arrays, which MariaDB doesn’t support.
- Simon also noted that JSON array aggregation is a broadly supported and standardized family of functionality across multiple database systems, so support should be added to Django core as a backend-agnostic aggregate (e.g., a `JSONArrayAgg` built by subclassing `Aggregate`), with backend-specific adjustments where needed (e.g., SQLite).

## Appendix

- [[MariaDB] ArrayAgg (and related functions) are missing from Django](https://jira.mariadb.org/browse/MDBF-690)
- [[MariaDB Docs] JSON_ARRAYAGG](https://mariadb.com/docs/server/reference/sql-functions/special-functions/json-functions/json_arrayagg)
- [[Django Docs] ArrayAgg](https://docs.djangoproject.com/en/dev/ref/contrib/postgres/aggregates/#arrayagg)
