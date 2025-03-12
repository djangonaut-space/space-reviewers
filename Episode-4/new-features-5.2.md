# 🚀 Space Reviewers 👾 - Episode 4

## Details

- Date: [2025-03-11 3pm UTC](https://time.is/compare/1500_11_March_2025_UTC)
- Stream: [https://youtu.be/EDOQcKT-9F0](https://youtu.be/EDOQcKT-9F0)

### Documentation
[Django 5.2 Release notes](https://docs.djangoproject.com/en/dev/releases/5.2/)

### Our favourite features

1. New warning when running `runserver`.
2. String representation of `HttpResponse.content`.
3. Automatic and customizable models import in the shell.
4. Control the running of system checks by overriding the new method`BaseCommand.get_check_kwargs()`.

### New warning while `runserver`
After a proposal on [Django Forum](https://forum.djangoproject.com/t/proposal-borrow-warning-from-werkzeug-for-runserver/32668/5) this new warning helps to avoid using the `runserver` in a developer server.
This warning can be hide adding this environment variable:
```shell
HIDE_PRODUCTION_WARNING=true
```

### `HttpResponse.text`
This new string `HttpResponse.text` will substitute the earlier representation of `HttpResponse.content` and will be decoded to UTF-8 by default.

### Automatic model import in the shell
Every time the shell will be open will automatically import all the models, even from the apps listed in `INSTALLED_APPS`.
To have a detailed list of which models will be imported use the flag `--verbosity=2` or `-v 2`

There will be also a chance to [customize this behavior](https://docs.djangoproject.com/en/dev/howto/custom-shell/#customizing-shell-auto-imports) by overriding the `get_auto_imports()` method.
Here an example:
```python
from django.core.management.commands import shell


class Command(shell.Command):
    def get_auto_imports(self):
        return super().get_auto_imports() + [
            "django.utils.timezone"
        ]
```

### Control the running of system checks
The method `check()` inspect the entire Django program for potential problem.
Now this new method `get_check_kwargs()` can be overridden in custom command to supplied `check()` new values and control the running of system checks.


### Episode notes
We receive lots of interesting suggestions from the audience, in particular from Baptiste, thank you so much.

1. Import in shell - when you want a customized behavior you're going to use `get_auto_imports`. But did you know that was renamed in 5.2a1 as `get_namespaces`?
    ```python
    from django.core.management.commands import shell
    
    
    class Command(shell.Command):
        def get_auto_imports(self):
            return super().get_auto_imports() + [
                "django.urls.reverse",
                "django.urls.resolve",
            ]
    ```
2. This is the [PR](https://github.com/django/django/commit/2ce4545de1791d6ed2405cb0657401e179bc5357#diff-47bba021b3e59f7fa8780295371d9a995dfbb832bbbd94cea4a859c51ae4542e) fot the new method `BaseCommand.get_check_kwargs()`
3. You can use the check command on a specific app, or a particular category using the flag `--tag` 
    ```
    django-admin check --tag models --tag compatibility
    ```
    for more information see the [check command documentation.](https://docs.djangoproject.com/en/5.1/ref/django-admin/#cmdoption-check-tag)
4. Here's a [list of tags](https://docs.djangoproject.com/en/5.1/ref/checks/#builtin-tags) for Django's system check
