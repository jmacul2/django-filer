============
django-filer
============


A file management application for django that makes handling of files and images a breeze.

Documentation: http://django-filer.readthedocs.org/en/latest/index.html

Wiki: https://github.com/stefanfoulis/django-filer/wiki

Dependencies
------------

* `Django`_ 1.2 with django-staticfiles or `Django`_ 1.3
* django-mptt >= 0.2.1
* `easy_thumbnails`_ >= 1.0-alpha-17
* `django-polymorphic`_ >= 0.2
* `PIL`_ 1.1.7 (with JPEG and ZLIB support)

Installation
------------

To get started using ``django-filer`` simply install it with
``pip``::

    $ pip install django-filer


Configuration
-------------

Add ``"filer"`` and ``"easy_thumbnails"`` to your project's ``INSTALLED_APPS`` setting and run ``syncdb``
(or ``migrate`` if you're using South).

For automatic subject location aware cropping of images replace 
``easy_thumbnails.processors.scale_and_crop`` with
``filer.thumbnail_processors.scale_and_crop_with_subject_location`` in the
``THUMBNAIL_PROCESSORS`` setting::

    THUMBNAIL_PROCESSORS = (
        'easy_thumbnails.processors.colorspace',
        'easy_thumbnails.processors.autocrop',
        #'easy_thumbnails.processors.scale_and_crop',
        'filer.thumbnail_processors.scale_and_crop_with_subject_location',
        'easy_thumbnails.processors.filters',
    )


.. _Django: http://djangoproject.com
.. _django-polymorphic: https://github.com/bconstantin/django_polymorphic
.. _easy_thumbnails: https://github.com/SmileyChris/easy-thumbnails
.. _sorl.thumbnail: http://thumbnail.sorl.net/
.. _PIL: http://www.pythonware.com/products/pil/
.. _Pillow: http://pypi.python.org/pypi/Pillow/
