Django reCAPTCHA
================
**Django reCAPTCHA form field/widget integration app.**

.. image:: https://travis-ci.org/tobiase/django-recaptcha.svg?branch=develop
    :target: https://travis-ci.org/tobiase/django-recaptcha

.. contents:: Contents
    :depth: 5

Django reCAPTCHA uses a modified version of the `Python reCAPTCHA client
<http://pypi.python.org/pypi/recaptcha-client>`_ which is included in the
package as ``client.py``.

Supported reCAPTCHA versions
----------------------------

Django reCAPTCHA supports reCAPTCHA v2,

Requirements
------------

Tested with:

* Python: 2.7, 3.5
* Django: 1.11, 2.0

Installation
------------

#. `Sign up for reCAPTCHA <https://www.google.com/recaptcha/intro/index.html>`_.

#. Install with ``pip install django-recaptcha``.

#. Add ``'captcha'`` to your ``INSTALLED_APPS`` setting.

#. Add the keys reCAPTCHA have given you to your Django production settings as
   ``RECAPTCHA_PUBLIC_KEY`` and ``RECAPTCHA_PRIVATE_KEY``. For example:

   .. code-block:: python

       RECAPTCHA_PUBLIC_KEY = 'MyRecaptchaKey123'
       RECAPTCHA_PRIVATE_KEY = 'MyRecaptchaPrivateKey456'

   These can also be specificied per field by passing the ``public_key`` or
   ``private_key`` parameters to ``ReCaptchaField`` - see field usage below.

 ``RECAPTCHA_PUBLIC_KEY`` and ``RECAPTCHA_PRIVATE_KEY`` must not be set in development settings if you want to use the default test keys.

#. If you require a proxy, add a ``RECAPTCHA_PROXY`` setting, for example:

   .. code-block:: python

       RECAPTCHA_PROXY = 'http://127.0.0.1:8000'

Usage
-----

Field
~~~~~

The quickest way to add reCAPTCHA to a form is to use the included
``ReCaptchaField`` field class. A ``ReCaptcha`` widget will be rendered with
the field validating itself without any further action required. For example:

.. code-block:: python

    from django import forms
    from captcha.fields import ReCaptchaField

    class FormWithCaptcha(forms.Form):
        captcha = ReCaptchaField()

To allow for runtime specification of keys you can optionally pass the
``private_key`` or ``public_key`` parameters to the constructor. For example:

.. code-block:: python

    captcha = ReCaptchaField(
        public_key='76wtgdfsjhsydt7r5FFGFhgsdfytd656sad75fgh',
        private_key='98dfg6df7g56df6gdfgdfg65JHJH656565GFGFGs',
    )

If specified these parameters will be used instead of your reCAPTCHA project
settings.

The reCAPTCHA widget supports several `Javascript options variables
<https://developers.google.com/recaptcha/docs/display#js_param>`_ that
customize the behaviour of the widget, such as ``theme`` and ``lang``. You can
forward these options to the widget by passing an ``attr`` parameter to the
field, containing a dictionary of options. For example:

.. code-block:: python

    captcha = ReCaptchaField(attrs={
      'theme' : 'clean',
    })

The client takes the key/value pairs and writes out the ``RecaptchaOptions``
value in JavaScript.


Local Development and Functional Testing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Google provides test keys which are set as the default for ``RECAPTCHA_PUBLIC_KEY`` and ``RECAPTCHA_PRIVATE_KEY``. These cannot be used in production since they always validate to true and a warning will be shown on the reCAPTCHA.

See: https://developers.google.com/recaptcha/docs/faq#id-like-to-run-automated-tests-with-recaptcha-what-should-i-do


Unit Testing
~~~~~~~~~~~~

Django reCAPTCHA introduces an environment variable ``RECAPTCHA_TESTING`` which
helps facilitate tests. The environment variable should be set to ``"True"``,
and cleared, using the ``setUp()`` and ``tearDown()`` methods in your test
classes.

Setting ``RECAPTCHA_TESTING`` to ``True`` causes Django reCAPTCHA to accept
``"PASSED"`` as the ``recaptcha_response_field`` value. Note that
the response field is called ``g-recaptcha-response``.

Example:

.. code-block:: python

    import os
    os.environ['RECAPTCHA_TESTING'] = 'True'

    form_params = {'g-recaptcha-response': 'PASSED'}
    form = RegistrationForm(form_params) # assuming only one ReCaptchaField
    form.is_valid() # True

    os.environ['RECAPTCHA_TESTING'] = 'False'
    form.is_valid() # False

Passing any other values will cause Django reCAPTCHA to continue normal
processing and return a form error.

Check ``tests.py`` for a full example.


AJAX
~~~~~

To make reCAPTCHA work in ajax-loaded forms:

#. Import ``recaptcha_ajax.js`` on your page (not in the loaded template):

   .. code-block:: html

       <script type="text/javascript" src="http://www.google.com/recaptcha/api/js/recaptcha_ajax.js"></script>

#. Add to your Django settings:

   .. code-block:: python

       CAPTCHA_AJAX = True


Development
-----------

.. code-block:: bash

    git clone git@github.com:tobiase/django-recaptcha.git
    cd django-recaptcha/
    virtualenv .venv
    source ./.venv/bin/activate
    pip install -e .[dev]

Translations
~~~~~~~~~~~~

Create translations template

.. code-block:: bash

    python setup.py extract_messages

Create translations for a language

.. code-block:: bash

    python setup.py init_catalog -l LANGUAGE

    # Example:
    python setup.py init_catalog -l de

Update translations for a language

.. code-block:: bash

    python setup.py update_catalog -l LANGUAGE

    # Example:
    python setup.py update_catalog -l de

Compile translations for a language

.. code-block:: bash

    python setup.py compile_catalog -l LANGUAGE

    # Example:
    python setup.py compile_catalog -l de

Code style
~~~~~~~~~~

The codes is formatted with black. The black formatting clashes at some points with pep8.

To format the code install black and run it.

.. code-block:: bash

    pip install black
    black .

Credits
-------
Inspired Marco Fucci's blogpost titled `Integrating reCAPTCHA with Django
<http://www.marcofucci.com/tumblelog/26/jul/2009/integrating-recaptcha-with-django>`_


``client.py`` taken from `recaptcha-client
<http://pypi.python.org/pypi/recaptcha-client>`_ licenced MIT/X11 by Mike
Crawford.

reCAPTCHA copyright 2012 Google.
