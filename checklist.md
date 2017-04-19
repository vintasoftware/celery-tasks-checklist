## Concept
- [ ] Check if your app follows [Unix philosophy](https://en.wikipedia.org/wiki/Unix_philosophy): "Do one thing, and do it well"

- [ ] Check if your app's description fits a few words
  * Examples:  
  [django-taggit](https://github.com/alex/django-taggit): django-taggit a simpler approach to tagging with Django.  
  [django-js-reverse](https://github.com/ierror/django-js-reverse): Javascript URL handling for Django that doesn't hurt.  
  [django-impersonate](https://bitbucket.org/petersanchez/django-impersonate): Simple application to allow superusers to "impersonate" other non-superuser accounts.  

- [ ] Check if your app's description has the word "and", if so, try to break it in more apps

## Easy to install
- [ ] Add a [LICENSE](https://choosealicense.com/) file

- [ ] [Distribute on PyPI](https://packaging.python.org/distributing/)
  * - [ ] Prefix name with django-
  * - [ ] Check for name clashes
    * There's the sad example of [django-filter](https://pypi.python.org/pypi/django-filter) vs [django-filters](https://pypi.python.org/pypi/django-filters)
  * - [ ] Use [Wheels](https://packaging.python.org/distributing/#wheels)

- [ ] Publish on [Django Packages](https://djangopackages.org/)

- [ ] Install dependencies automatically
  * - [ ] Add dependencies on `install_requires` on setup.py
  * - [ ] Don't add Django to `install_requires`
  * - [ ] Don't pin versions with `==` on `install_requires`. Use `>=`

- [ ] Check if you need a Django app or a regular Python package
  * Django apps need to be added to `INSTALLED_APPS`. Regular Python packages do not. Examples of regular Python packages:  
  [django-templated-email](https://github.com/vintasoftware/django-templated-email)  
  [django-hashid-field](https://github.com/nshafer/django-hashid-field)  
  [django-downloadview](https://github.com/benoitbryon/django-downloadview)  

- [ ] Have sane and smart defaults
  * - [ ] Make it work by default
  * - [ ] Don't require copy and pasting of code snippets
  * - [ ] Don't do anything dangerous by default, like caching

- [ ] Require unsafe behavior to be explicit
  * Don't show all if something isn't set, e.g., `fields = None` shouldn't mean all fields

- [ ] Have declarative settings to allow easy configuration
  * - [ ] Add a prefix to all settings of the app, like `MYAPP_SETTING_KEY`
  * - [ ] Convert hardcoded internal parameters to settings
    * For example, `AVATAR_MAX_SIZE` of [django-avatar](http://django-avatar.readthedocs.io/en/latest/#AVATAR_MAX_SIZE) could be hardcoded, but it's a setting
  * - [ ] If needed frequently by developers, allow behavior to be changed with just a change of settings
  * - [ ] If needed frequently by developers, accept custom classes and functions on settings via dotted path
    * For example, [django-taggit](https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#using-a-custom-tag-string-parser) supports custom tag parsers via settings

- [ ] Support declarative pipelines for configurable workflows
  * Check a implementation at [python-social-auth](http://python-social-auth-docs.readthedocs.io/en/latest/configuration/django.html#personalized-configuration)

- [ ] Provide default views with templates and URLs to allow the app to be easily included

- [ ] Have a friendly upgrade policy
  * - [ ] Deprecate before removing. Raise deprecation warnings, use Python [warnings](https://docs.python.org/3/library/warnings.html) built-in module
  * - [ ] Don't rewrite migrations
    * Users might depend on your old migration files because they ran it against their data in the past
  * - [ ] [Keep a CHANGELOG](http://keepachangelog.com/)
  * - [ ] Follow [Semantic Versioning](http://semver.org/)

- [ ] Give credit, have a [AUTHORS file](https://github.com/django/django/blob/master/AUTHORS)
  * Check this [script](https://kev.inburke.com/kevin/easy-maintenance-of-your-authors-file/) to generate AUTHORS file from git history

## Easy to use
- [ ] Provide documentation
  * - [ ] Write docs first
  * - [ ] Have a README
  * - [ ] Provide a quick start tutorial describing the most common use case
  * - [ ] Separate high level from low-level docs
  * - [ ] Use gender neutral pronouns
  * - [ ] Host it in [Read the Docs](http://readthedocs.org/)

- [ ] Write tests
  * - [ ] Test with a [custom user model](https://docs.djangoproject.com/en/dev/topics/auth/customizing/#specifying-a-custom-user-model)
  * - [ ] Provide [coverage](http://coverage.readthedocs.io/en/latest/)

- [ ] Have Continuous Integration
  * - [ ] Use [tox](https://tox.readthedocs.io/en/latest/) to test against various Python and Django versions
    * Check tox.ini of popular projects like [django-filter](https://github.com/carltongibson/django-filter/blob/develop/tox.ini) and [django-taggit](https://github.com/alex/django-taggit/blob/master/tox.ini)
  * - [ ] Respect PEP 8, use [flake8](https://gitlab.com/pycqa/flake8)

- [ ] Ship with an example project
  * Examples of implementations:  
  [django-guardian](https://github.com/django-guardian/django-guardian/tree/devel/example_project)
  [django-haystack](https://github.com/django-haystack/django-haystack/tree/master/example_project)
  [django-avatar](https://github.com/grantmcconnaughey/django-avatar/tree/master/test_proj)

- [ ] Separate Django abstractions into [commonly used filenames](https://gist.github.com/fjsj/65ecfec09cfd2a684d53294d01677b9b) like views.py, forms.py, fields.py, etc.

- [ ] Follow the pattern `resource_action` in URLs names, like `password_reset` or `product_detail`

- [ ] Never rely on Django default user model, support [custom user models](https://docs.djangoproject.com/en/dev/topics/auth/customizing/#specifying-a-custom-user-model)
  * Check a implementation at [django-registration](http://django-registration.readthedocs.io/en/latest/custom-user.html)

- [ ] Provide declarative usage
  * Examples of implementations:  
  [django-filter filtersets](https://django-filter.readthedocs.io/en/develop/guide/usage.html#generating-filters-with-meta-fields)   
  [django-import-export resources](http://django-import-export.readthedocs.io/en/latest/getting_started.html#advanced-data-manipulation)

- [ ] Don't connect code implicitly by name or module. Have a registry
  * Examples of implementations:  
  [django-contrib-comments moderator registry](http://django-contrib-comments.readthedocs.io/en/latest/moderation.html#module-django_comments.moderation)  
  [django-activity-stream registry](http://django-activity-stream.readthedocs.io/en/latest/configuration.html)

- [ ] Have management commands for common developer needs
  * Examples of implementations:  
  [django-reversion commands](http://django-reversion.readthedocs.io/en/latest/commands.html)  
  [django-haystack commands](http://django-haystack.readthedocs.io/en/latest/tutorial.html)

- [ ] Use `_default_manager`, not `objects`, when interacting with models of the host project

- [ ] Be Pythonic
  * - [ ] Use generators for lazy evaluation
  * - [ ] Use `with` statement contexts to deal with unmanaged resources

- [ ] Prevent errors
  * - [ ] Provide checks with [Django System check framework](https://docs.djangoproject.com/en/dev/topics/checks/)

- [ ] Fail-fast
  * - [ ] Raise `ImproperlyConfigured` if the developer makes a mistake on the config
    * For example, [django-filter raises `ImproperlyConfigured`](https://github.com/carltongibson/django-filter/blob/0883cb6b25cd3bb2fa337fb9c54f0a3d2159f676/django_filters/views.py#L18-L28) when the developer forgets to specify `filterset_class` or `model` in a `FilterView`
  * - [ ] Raise `TypeError` or `ValueError` when the app gets an invalid argument

- [ ] [Internationalize](https://docs.djangoproject.com/en/dev/topics/i18n/translation/) (I18N) your strings

## Easy to integrate
- [ ] Reduce integration discontinuities
  * - [ ] Break class behaviors into methods
  * - [ ] Separate class behaviors into mixins
  * - [ ] Isolate logic into helper modules with business functions and classes

- [ ] [Use `AppConfig`](https://docs.djangoproject.com/en/dev/ref/applications/)
  * - [ ] Make sure app doesn't break when AppConfig is extended

- [ ] Provide default templates
  * - [ ] Don't put them directly into `templates/`, put into `templates/app_name/`
  * - [ ] Guarantee they can be changed by loading a custom one with the same path

- [ ] Provide template tags for presenting complex data
  * - [ ] Leave only presentation logic into template tags, break the rest of logic into helpers
    * For example, django-avatar has a `avatar` template tag to generate HTML `img` tags, but the logic to generate avatar URLs is [isolated at `providers.py`](https://github.com/grantmcconnaughey/django-avatar/blob/master/avatar/providers.py)

- [ ] Provide default views
  * - [ ] Don't break the configurability of class-based views, allow existing Django views attrs and methods to be overridden
  * - [ ] Break views common logic into mixins

- [ ] Avoid built-in models if possible
  * - [ ] If you need to have them, provide an abstract model and allow your app to work with a concrete custom variety
    * Examples of implementations:  
    [django-taggit custom tag model](https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#custom-tag)  
    [django-blog-zinnia custom entry model](http://docs.django-blog-zinnia.com/en/develop/how-to/extending_entry_model.html)  
    [django-contrib-comments custom app](https://django-contrib-comments.readthedocs.io/en/latest/custom.html)
  * - [ ] Break models common parts into abstract models
  * - [ ] Don't use model mixins, use abstract models
    * Check the reason on this [StackOverflow answer](http://stackoverflow.com/a/25817237/145349)
  * - [ ] When using Generic Foreign Keys, allow them to be overridden by direct FKs
    * Examples of implementations:  
    [django-guardian support of direct foreign keys](http://django-guardian.readthedocs.io/en/latest/userguide/performance.html#direct-foreign-keys)  
    [django-taggit support of custom through model](https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#custom-foreignkeys)

- [ ] Isolate form field logic into form fields and widgets
  * Check a implementation at [django-recaptcha](https://github.com/praekelt/django-recaptcha)

- [ ] Isolate model field logic into model fields
  * Check a implementation at [django-hashid-field](https://github.com/nshafer/django-hashid-field)

- [ ] Isolate query logic into queryset methods, like filter, update and delete logic

- [ ] Isolate table-level behavior logic into managers methods, like create logic

- [ ] Isolate validation logic into validators

- [ ] Use context processors only for global logic

- [ ] Use middlewares only for global logic related to request-response cycle or current user

- [ ] Avoid signals spaghetti code

## Maintenance
- [ ] Be transparent about bugs, especially security issues
  * - [ ] Add security warnings to CHANGELOG, make sure they're parseable by [safety](https://github.com/pyupio/safety) tool

- [ ] Don't abandon the project, give it away
