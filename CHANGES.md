# Changelog and versioning

## Versioning Policy

This project follows [semver](https://semver.org/), with three additions:

* Semver says that major version `0` can include breaking changes at any time.
  Still, it is common practice to assume that only `0.x` releases (minor
  versions) can contain breaking changes while `0.x.y` releases (patch
  versions) are used for backwards-compatible changes (bugfixes and features).
  This project also follows that practice.

* All undocumented APIs are considered internal. They are not part of this
  contract.

* Certain features (e.g. integrations) may be explicitly called out as
  "experimental" or "unstable" in the documentation. They come with their own
  versioning policy described in the documentation.

We recommend to pin your version requirements against `0.x.*` or `0.x.y`.
Either one of the following is fine:

```
sentry-sdk>=0.10.0,<0.11.0
sentry-sdk==0.10.1
```

A major release `N` implies the previous release `N-1` will no longer receive updates. We generally do not backport bugfixes to older versions unless they are security relevant. However, feel free to ask for backports of specific commits on the bugtracker.

## 0.11.1

* Remove a faulty assertion (observed in environment with Django Channels and ASGI).

## 0.11.0

* Fix type hints for the logging integration. Thansk Steven Dignam!
* Fix an issue where scope/context data would leak in applications that use `gevent` with its threading monkeypatch. The fix is to avoid usage of contextvars in such environments. Thanks Ran Benita!
* Fix a reference cycle in the `ThreadingIntegration` that led to exceptions on interpreter shutdown. Thanks Guang Tian Li!
* Fix a series of bugs in the stdlib integration that broke usage of `subprocess`.
* More instrumentation for APM.
* New integration for SQLAlchemy (creates breadcrumbs from queries).
* New (experimental) integration for Apache Beam.
* Fix a bug in the `LoggingIntegration` that would send breadcrumbs timestamps in the wrong timezone.
* The `AiohttpIntegration` now sets the event's transaction name.
* Fix a bug that caused infinite recursion when serializing local variables that logged errors or otherwise created Sentry events.

## 0.10.2

* Fix a bug where a log record with non-strings as `extra` keys would make the SDK crash.
* Added ASGI integration for better hub propagation, request data for your events and capturing uncaught exceptions. Using this middleware explicitly in your code will also fix a few issues with Django Channels.
* Fix a bug where `celery-once` was deadlocking when used in combination with the celery integration.
* Fix a memory leak in the new tracing feature when it is not enabled.

## 0.10.1

* Fix bug where the SDK would yield a deprecation warning about
  `collections.abc` vs `collections`.
* Fix bug in stdlib integration that would cause spawned subprocesses to not
  inherit the environment variables from the parent process.

## 0.10.0

* Massive refactor in preparation to tracing. There are no intentional breaking
  changes, but there is a risk of breakage (hence the minor version bump). Two
  new client options `traces_sample_rate` and `traceparent_v2` have been added.
  Do not change the defaults in production, they will bring your application
  down or at least fill your Sentry project up with nonsense events.

## 0.9.5

* Do not use ``getargspec`` on Python 3 to evade deprecation
  warning.

## 0.9.4

* Revert a change in 0.9.3 that prevented passing a ``unicode``
  string as DSN to ``init()``.

## 0.9.3

* Add type hints for ``init()``.
* Include user agent header when sending events.

## 0.9.2

* Fix a bug in the Django integration that would prevent the user
  from initializing the SDK at the top of `settings.py`.

  This bug was introduced in 0.9.1 for all Django versions, but has been there
  for much longer for Django 1.6 in particular.

## 0.9.1

* Fix a bug on Python 3.7 where gunicorn with gevent would cause the SDK to
  leak event data between requests.
* Fix a bug where the GNU backtrace integration would not parse certain frames.
* Fix a bug where the SDK would not pick up request bodies for Django Rest
  Framework based apps.
* Remove a few more headers containing sensitive data per default.
* Various improvements to type hints. Thanks Ran Benita!
* Add a event hint to access the log record from `before_send`.
* Fix a bug that would ignore `__tracebackhide__`. Thanks Matt Millican!
* Fix distribution information for mypy support (add `py.typed` file). Thanks
  Ran Benita!

## 0.9.0

* The SDK now captures `SystemExit` and other `BaseException`s when coming from
  within a WSGI app (Flask, Django, ...)
* Pyramid: No longer report an exception if there exists an exception view for
  it.

## 0.8.1

* Fix infinite recursion bug in Celery integration.

## 0.8.0

* Add the always_run option in excepthook integration.
* Fix performance issues when attaching large data to events. This is not
  really intended to be a breaking change, but this release does include a
  rewrite of a larger chunk of code, therefore the minor version bump.

## 0.7.14

* Fix crash when using Celery integration (`TypeError` when using
  `apply_async`).

## 0.7.13

* Fix a bug where `Ignore` raised in a Celery task would be reported to Sentry.
* Add experimental support for tracing PoC.

## 0.7.12

* Read from `X-Real-IP` for user IP address.
* Fix a bug that would not apply in-app rules for attached callstacks.
* It's now possible to disable automatic proxy support by passing
  `http_proxy=""`. Thanks Marco Neumann!

## 0.7.11

* Fix a bug that would send `errno` in an invalid format to the server.
* Fix import-time crash when running Python with `-O` flag.
* Fix a bug that would prevent the logging integration from attaching `extra`
  keys called `data`.
* Fix order in which exception chains are reported to match Raven behavior.
* New integration for the Falcon web framework. Thanks to Jacob Magnusson!

## 0.7.10

* Add more event trimming.
* Log Sentry's response body in debug mode.
* Fix a few bad typehints causing issues in IDEs.
* Fix a bug in the Bottle integration that would report HTTP exceptions (e.g.
  redirects) as errors.
* Fix a bug that would prevent use of `in_app_exclude` without
  setting `in_app_include`.
* Fix a bug where request bodies of Django Rest Framework apps were not captured.
* Suppress errors during SQL breadcrumb capturing in Django
  integration. Also change order in which formatting strategies
  are tried.

## 0.7.9

* New integration for the Bottle web framework. Thanks to Stepan Henek!
* Self-protect against broken mapping implementations and other broken reprs
  instead of dropping all local vars from a stacktrace. Thanks to Marco
  Neumann!

## 0.7.8

* Add support for Sanic versions 18 and 19.
* Fix a bug that causes an SDK crash when using composed SQL from psycopg2.

## 0.7.7

* Fix a bug that would not capture request bodies if they were empty JSON
  arrays, objects or strings.
* New GNU backtrace integration parses stacktraces from exception messages and
  appends them to existing stacktrace.
* Capture Tornado formdata.
* Support Python 3.6 in Sanic and AIOHTTP integration.
* Clear breadcrumbs before starting a new request.
* Fix a bug in the Celery integration that would drop pending events during
  worker shutdown (particularly an issue when running with `max_tasks_per_child
  = 1`)
* Fix a bug with `repr`ing locals whose `__repr__` simultaneously changes the
  WSGI environment or other data that we're also trying to serialize at the
  same time.

## 0.7.6

* Fix a bug where artificial frames for Django templates would not be marked as
  in-app and would always appear as the innermost frame. Implement a heuristic
  to show template frame closer to `render` or `parse` invocation.

## 0.7.5

* Fix bug into Tornado integration that would send broken cookies to the server.
* Fix a bug in the logging integration that would ignore the client
  option `with_locals`.

## 0.7.4

* Read release and environment from process environment like the Raven SDK
  does. The keys are called `SENTRY_RELEASE` and `SENTRY_ENVIRONMENT`.
* Fix a bug in the `serverless` integration where it would not push a new scope
  for each function call (leaking tags and other things across calls).
* Experimental support for type hints.

## 0.7.3

* Fix crash in AIOHTTP integration when integration was set up but disabled.
* Flask integration now adds usernames, email addresses based on the protocol
  Flask-User defines on top of Flask-Login.
* New threading integration catches exceptions from crashing threads.
* New method `flush` on hubs and clients. New global `flush` function.
* Add decorator for serverless functions to fix common problems in those
  environments.
* Fix a bug in the logging integration where using explicit handlers required
  enabling the integration.

## 0.7.2

* Fix `celery.exceptions.Retry` spamming in Celery integration.

## 0.7.1

* Fix `UnboundLocalError` crash in Celery integration.

## 0.7.0

* Properly display chained exceptions (PEP-3134).
* Rewrite celery integration to monkeypatch instead of using signals due to
  bugs in Celery 3's signal handling. The Celery scope is also now available in
  prerun and postrun signals.
* Fix Tornado integration to work with Tornado 6.
* Do not evaluate Django `QuerySet` when trying to capture local variables.
  Also an internal hook was added to overwrite `repr` for local vars.

## 0.6.9

* Second attempt at fixing the bug that was supposed to be fixed in 0.6.8.

  > No longer access arbitrary sequences in local vars due to possible side effects.

## 0.6.8

* No longer access arbitrary sequences in local vars due to possible side effects.

## 0.6.7

* Sourcecode Django templates is now displayed in stackframes like Jinja templates in Flask already were.
* Updates to AWS Lambda integration for changes Amazon did to their Python 3.7 runtime.
* Fix a bug in the AIOHTTP integration that would report 300s and other HTTP status codes as errors.
* Fix a bug where a crashing `before_send` would crash the SDK and app.
* Fix a bug where cyclic references in e.g. local variables or `extra` data would crash the SDK.

## 0.6.6

* Un-break API of internal `Auth` object that we use in Sentry itself.

## 0.6.5

* Capture WSGI request data eagerly to save memory and avoid issues with uWSGI.
* Ability to use subpaths in DSN.
* Ignore `django.request` logger.

## 0.6.4

* Fix bug that would lead to an `AssertionError: stack must have at least one layer`, at least in testsuites for Flask apps.

## 0.6.3

* New integration for Tornado
* Fix request data in Django, Flask and other WSGI frameworks leaking between events.
* Fix infinite recursion when sending more events in `before_send`.

## 0.6.2

* Fix crash in AWS Lambda integration when using Zappa. This only silences the error, the underlying bug is still in Zappa.

## 0.6.1

* New integration for aiohttp-server.
* Fix crash when reading hostname in broken WSGI environments.

## 0.6.0

* Fix bug where a 429 without Retry-After would not be honored.
* Fix bug where proxy setting would not fall back to `http_proxy` for HTTPs traffic.
* A WSGI middleware is now available for catching errors and adding context about the current request to them.
* Using `logging.debug("test", exc_info=True)` will now attach the current stacktrace if no `sys.exc_info` is available.
* The Python 3.7 runtime for AWS Lambda is now supported.
* Fix a bug that would drop an event or parts of it when it contained bytes that were not UTF-8 encoded.
* Logging an exception will no longer add the exception as breadcrumb to the exception's own event.

## 0.5.5

* New client option `ca_certs`.
* Fix crash with Django and psycopg2.

## 0.5.4

* Fix deprecation warning in relation to the `collections` stdlib module.
* Fix bug that would crash Django and Flask when streaming responses are failing halfway through.

## 0.5.3

* Fix bug where using `push_scope` with a callback would not pop the scope.
* Fix crash when initializing the SDK in `push_scope`.
* Fix bug where IP addresses were sent when `send_default_pii=False`.

## 0.5.2

* Fix bug where events sent through the RQ integration were sometimes lost.
* Remove a deprecation warning about usage of `logger.warn`.
* Fix bug where large frame local variables would lead to the event being rejected by Sentry.

## 0.5.1

* Integration for Redis Queue (RQ)

## 0.5.0

* Fix a bug that would omit several debug logs during SDK initialization.
* Fix issue that sent a event key `""` Sentry wouldn't understand.
* **Breaking change:** The `level` and `event_level` options in the logging integration now work separately from each other.
* Fix a bug in the Sanic integration that would report the exception behind any HTTP error code.
* Fix a bug that would spam breadcrumbs in the Celery integration. Ignore logger `celery.worker.job`.
* Additional attributes on log records are now put into `extra`.
* Integration for Pyramid.
* `sys.argv` is put into extra automatically.

## 0.4.3

* Fix a bug that would leak WSGI responses.

## 0.4.2

* Fix a bug in the Sanic integration that would leak data between requests.
* Fix a bug that would hide all debug logging happening inside of the built-in transport.
* Fix a bug that would report errors for typos in Django's shell.

## 0.4.1

* Fix bug that would only show filenames in stacktraces but not the parent
  directories.

## 0.4.0

* Changed how integrations are initialized. Integrations are now
  configured and enabled per-client.

## 0.3.11

* Fix issue with certain deployment tools and the AWS Lambda integration.

## 0.3.10

* Set transactions for Django like in Raven. Which transaction behavior is used
  can be configured.
* Fix a bug which would omit frame local variables from stacktraces in Celery.
* New option: `attach_stacktrace`

## 0.3.9

* Bugfixes for AWS Lambda integration: Using Zappa did not catch any exceptions.

## 0.3.8

* Nicer log level for internal errors.

## 0.3.7

* Remove `repos` configuration option. There was never a way to make use of
  this feature.
* Fix a bug in `last_event_id`.
* Add Django SQL queries to breadcrumbs.
* Django integration won't set user attributes if they were already set.
* Report correct SDK version to Sentry.

## 0.3.6

* Integration for Sanic

## 0.3.5

* Integration for AWS Lambda
* Fix mojibake when encoding local variable values

## 0.3.4

* Performance improvement when storing breadcrumbs

## 0.3.3

* Fix crash when breadcrumbs had to be trunchated

## 0.3.2

* Fixed an issue where some paths where not properly sent as absolute paths
