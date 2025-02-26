# 🚀 Space Reviewers 👾 - Episode 3

## Details

- Date: [2025-03-11 3pm UTC](https://time.is/compare/1500_11_March_2025_UTC)
- Stream: Live at https://www.youtube.com/@djangonautspace

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



