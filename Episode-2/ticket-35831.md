# 🚀 Space Reviewers 👾 - Episode 2

## Details

- Date: [2024-12-03 5pm UTC](https://time.is/compare/1700_3_December_2024_UTC)
- Ticket: [#35831](https://code.djangoproject.com/ticket/35831)
- PR: [#18699](https://github.com/django/django/pull/18699)
- Stream: Live at https://www.youtube.com/@djangonautspace

## Problem

The ticket is looking to add reference documentation for `ModelForm` and
its `Meta` class. Some of this documentation exists in topics, but there are
details that are specifically reference documentation that are missing.

## Summarized issue discussion

1. From ticket reporter, jernwerber:
   > As a developer, it would be helpful to be able to have a consolidated reference for the complete set of possible Meta options (ModelFormOptions) that can be used with a ModelForm's Meta class in a single place, rather than having to infer options from other topics or check the Python source.
2. [Clifford agreed](https://code.djangoproject.com/ticket/35831#comment:2) this
   something that should be documented, but expressed concern about overlap
   with [Topic documentation](https://docs.djangoproject.com/en/5.1/topics/forms/modelforms/).
3. [jernwerber expressed concern](https://code.djangoproject.com/ticket/35831#comment:4)
   about where to put the documentation. This is because the
   existing reference docs are written for Model Form Functions and isn't broad enough
   to cover `ModelForm` explicitly.

## Appendix

### Documentation

- [Model form topic docs](https://docs.djangoproject.com/en/5.1/topics/forms/modelforms/)
- [Model form reference docs](https://docs.djangoproject.com/en/5.1/ref/forms/models/)

### Other topics

- [Diátaxis](https://diataxis.fr/) documentation framework