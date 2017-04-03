- Concept
-- Check if your app follows Unix philosophy: "Do one thing, and do it well"

-- Check if your app's description fits a few words
* https://github.com/alex/django-taggit: django-taggit a simpler approach to tagging with Django.
* https://github.com/ierror/django-js-reverse: Javascript URL handling for Django that doesn't hurt.
* https://bitbucket.org/petersanchez/django-impersonate: Simple application to allow superusers to "impersonate" other non-superuser accounts.

-- Check if your app's description has the word "and", is so, try to break it in more apps

- Easy to install
-- Add a LICENSE file

-- Make available on PyPI
--- Prefix with django-
--- Check for name clashes
--- Use Wheels

-- Publish on Django Packages

-- Install dependencies automatically
--- Add dependencies on install_requires on setup.py
--- Don't add Django to install_requires
--- Don't pin versions with == on install_requires. Use >=

-- Check if you need a Django app or a regular Python package
* Regular Python package examples:
https://github.com/vintasoftware/django-templated-email
https://github.com/nshafer/django-hashid-field
https://github.com/benoitbryon/django-downloadview

-- Have sane and smart defaults
--- Make it work by default
--- Don't require copy and pasting of code snippets
--- Don't do anything dangerous by default, like caching

-- Require unsafe behavior to be explicit
--- Don't show all if something isn't set, e.g., fields == None shouldn't mean all fields

-- Have declarative settings to allow easy configuration
--- Add a prefix to all settings of the app
--- Convert hardcoded internal parameters to settings
--- If needed frequently by developers, allow behavior to be changed with just a change of settings
--- If needed frequently by developers, accept custom classes and functions on settings via dotted path
* See: https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#using-a-custom-tag-string-parser

-- Support declarative pipelines

-- Provide default views with templates and URLs to allow the app to be easily included

-- Have a friendly upgrade policy
--- Deprecate before removing. Raise deprecation warnings, use Python warnings built-in module.
--- Don't rewrite migrations.
--- Keep a CHANGELOG
--- Follow Semantic Versioning

-- Give credit, have a AUTHORS file

- Easy to use
-- Provide documentation
--- Write docs first
--- Have a README
--- Provide a quick start tutorial describing the most common use case
--- Separate high level from low-level docs
--- Use gender neutral pronouns

-- Write tests

-- Have Continuous Integration
--- Provide coverage
--- Use tox, test against various Django versions
--- Test with custom user model
--- Respect PEP 8

-- Ship with an example project

-- Separate Django abstractions into commonly used filenames
* https://gist.github.com/fjsj/65ecfec09cfd2a684d53294d01677b9b

-- Follow the pattern resource_action in URLs names, like password_reset or product_detail

-- Never rely on Django default user model
* Example of lib that supports custom user models: https://django-registration.readthedocs.io/en/2.2/custom-user.html

-- Provides declarative usage
* Examples of libs:
https://django-filter.readthedocs.io/en/develop/guide/usage.html#generating-filters-with-meta-fields
http://django-import-export.readthedocs.io/en/stable/getting_started.html#advanced-data-manipulation

-- Don't connect code implicitly by name or module. Have a registry
* Examples of libs:
http://django-contrib-comments.readthedocs.io/en/latest/moderation.html#module-django_comments.moderation
http://django-activity-stream.readthedocs.io/en/latest/configuration.html

-- Have management commands for common developer needs
* Examples of libs:
https://django-reversion.readthedocs.io/en/stable/commands.html
http://django-haystack.readthedocs.io/en/v2.6.0/tutorial.html#reindex

-- Use _default_manager, not objects, when interacting with models of the host project

-- Be Pythonic
--- Use generators for lazy evaluation
--- Use with-statement contexts to deal with unmanaged resources

-- Prevent errors
--- Provide checks with [Django System check framework](https://docs.djangoproject.com/en/1.10/topics/checks/)

-- Fail-fast
--- Raise ImproperlyConfigured if the developer makes a mistake on the config
--- Raise TypeError or ValueError when the app gets an invalid argument

-- Internationalize (I18N) your strings

- Easy to integrate
-- Reduce integration discontinuities
--- Break class behaviors into methods
--- Separate class behaviors into mixins
--- Isolate logic into helper modules with business functions and classes

-- Use AppConfig
--- Make sure app doesn't break when AppConfig is extended

-- Provide default templates
--- Don't put them directly into templates/, put into templates/app_name/
--- Guarantee they can be changed by loading a custom one with the same path

-- Provide template tags for presenting complex data
--- Leave only presentation logic into template tags, break the rest of logic into helpers

-- Provide default views
--- Don't break the configurability of class-based views, allow existing Django views attrs and methods to be overridden
--- Break views common logic into mixins

-- Avoid built-in models if possible
--- If you need to have them, provide an abstract model and allow your app to work with a concrete custom variety
* Examples of libs:
https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#custom-tag
http://docs.django-blog-zinnia.com/en/develop/how-to/extending_entry_model.html
https://django-contrib-comments.readthedocs.io/en/latest/custom.html
--- Break models common parts into abstract models
--- Don't use model mixins, use abstract models
* Why: http://stackoverflow.com/questions/3254436/django-model-mixins-inherit-from-models-model-or-from-object
--- When using Generic Foreign Keys, allow them to be overridden by direct FKs
* Examples of libs:
https://django-guardian.readthedocs.io/en/stable/userguide/performance.html#direct-foreign-keys
https://django-taggit.readthedocs.io/en/latest/custom_tagging.html#using-a-custom-tag-or-through-model

-- Isolate form field logic into form fields and widgets
* Example of lib: https://github.com/praekelt/django-recaptcha

-- Isolate model field logic into model fields
* Example of lib: https://github.com/nshafer/django-hashid-field

-- Isolate query logic into queryset methods, like filter, update and delete logic

-- Isolate table-level behavior logic into managers methods, like create logic

-- Isolate validation logic into validators

-- Use context_processors only for global logic

-- Use middlewares only for global logic related to request-response cycle or current user

-- Avoid signals spaghetti code

- App maintenance
-- Be transparent about bugs, especially security issues
--- Add security warnings to CHANGELOG, make sure they're parseable by [safety tool](https://github.com/pyupio/safety)

-- Don't abandon the project, give it away
