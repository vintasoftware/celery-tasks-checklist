- Concept
-- Check if your app follows Unix philosophy: "Do one thing, and do it well"

-- Check if your app's description fits a few words

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
--- Separate high level from low level docs
--- Use gender neutral pronouns

-- Write tests

-- Have Continuous Integration
--- Provide coverage
--- Use tox, test against various Django versions
--- Test with custom user model
--- Respect PEP 8

-- Ship with an example project

-- Use Django abstractions, strive for "Recognition rather than recall"

-- Never rely on Django default user model

-- Provides declarative usage

-- Don't import things implicitly by name or module, have a registry

-- Have management commands for common developer needs

-- Use _default_manager if you interact with client models

-- Be Pythonic
--- Use generators for lazy evaluation
--- Use with-statement contexts to deal with unmanaged resources

-- Prevent errors
--- Provide checks with Django System check framework

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
--- Don't break the configurability of class-based views
--- Break views common logic into mixins

-- Avoid built-in models if possible
--- If you need to have them, provide an abstract model and allow your app to work with the a concrete custom variety
--- Break models common parts into abstract models
--- Don't use model mixins, use abstract models
--- When using Generic Foreign Keys, allow them to be overriden by concrete FKs

-- Isolate form field logic into form fields and widgets

-- Isolate model field logic into model fields

-- Isolate filter logic into queryset methods

-- Isolate behavior logic into managers methods

-- Isolate validation logic into validators

-- Use context_processors only for global logic

-- Use middlewares only for global logic related to request-response cycle or current user

-- Avoid signals spaghetti code

- App maintenance
-- Be transparent about bugs, specially security issues
--- Add security warnings to CHANGELOG, make sure they're parseable by safety tool

-- Don't abandon the project, give it away
