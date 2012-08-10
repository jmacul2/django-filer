.. _server:

File Server Backends
====================

.. note:: Please follow the instructions for setting up :ref:`permissions` first.

.. warning:: Server Backends are experimental and the API may change at any time.

.. warning:: Server Backends currently only work with files in the local filesystem.

The private file view will serve the permission-checked media files by
delegating to one of its server backends. The ones bundled with django-filer
live in ``filer.server.backends`` and it is easy to create new ones.

The default is ``filer.server.backends.default.DefaultServer``. It is suitable
for development and serves the file directly from django.

More suitiable for production are server backends that delegate the actual file
serving to an upstream webserver.

``NginxXAccelRedirectServer``
-----------------------------

location: ``filer.server.backends.nginx.NginxXAccelRedirectServer``

nginx docs about this stuff: http://wiki.nginx.org/XSendfile

in ``settings.py``::

    FILER_SERVERS = {
        'private': {
            'main': {
                'ENGINE': 'filer.server.backends.nginx.NginxXAccelRedirectServer',
                'OPTIONS': {
                    'location': '/path/to/smedia/filer',
                    'nginx_location': '/nginx_filer_private',
                },
            },
            'thumbnails': {
                'ENGINE': 'filer.server.backends.nginx.NginxXAccelRedirectServer',
                'OPTIONS': {
                    'location': '/path/to/smedia/filer_thumbnails',
                    'nginx_location': '/nginx_filer_private_thumbnails',
                },
            },
        },
    }


``nginx_location`` is the location directive where nginx "hides"
permission-checked files from general access. A fitting nginx configuration
might look something like this::
    
    location /nginx_filer_private/ {
      internal;
      alias /path/to/smedia/filer_private/;
    }
    location /nginx_filer_private_thumbnails/ {
      internal;
      alias /path/to/smedia/filer_private_thumbnails/;
    }

.. Note::
   make sure you follow the example exactly. Missing trailing slashes and
   ``alias`` vs. ``root`` have subtle differences that can make your config
   fail.

``NginxXAccelRedirectServer`` will add the a ``X-Accel-Redirect`` header to 
the response instead of actually loading and delivering the file itself. The 
value in the header will be something like 
``/nginx_filer_private/2011/03/04/myfile.pdf``. Nginx picks this up and does
the actual file delivery while the django backend is free to do other stuff
again.

``ApacheXSendfileServer``
-------------------------

location: ``filer.server.backends.xsendfile.ApacheXSendfileServer``

.. Warning::
   I have not tested this myself. Any feedback and example configurations are
   very welcome :-)

Once you have ``mod_xsendfile`` installed on your apache server you can
configure the settings.

in ``settings.py``::
    
    FILER_SERVERS = {
        'private': {
            'main': {
                'ENGINE': 'filer.server.backends.xsendfile.ApacheXSendfileServer',
                },
            'thumbnails': {
                'ENGINE': 'filer.server.backends.xsendfile.ApacheXSendfileServer',
                },
            },
        }

in your apache configuration::
    
    XSendFilePath /path/to/smedia/

``XSendFilePath`` is a whitelist for directories where apache will serve files
from.
