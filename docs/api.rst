===
API
===

dpaste provides a simple API endpoint to create new snippets. All you need to
do is a simple ``POST`` request to the API endpoint ``/api/``::


    POST http://dpaste.de/api/


Available POST data for an API call:
====================================

``content`` (required)
~~~~~~~~~~~~~~~~~~~~~~

Required. The UTF-8 encoded string you want to paste.

``lexer`` (optional)
~~~~~~~~~~~~~~~~~~~~

Optional. Can also be set via GET. The lexer string key used for highlighting.
See `lexer list`_  for a full list of choices. Default: ``python``.

``format`` (optional)
~~~~~~~~~~~~~~~~~~~~~

Optional. Can also be set via GET. The format of the API response. Choices are:

* ``default`` — Returns a full qualified URL wrapped in quotes. Example::

    "https://dpaste.de/xsWd"

* ``url`` — Returns the full qualified URL to the snippet, without breaks,
  but with a line break. Example::

    https://dpaste.de/xsWd\n

* ``json`` — Returns a JSON object containing the URL, lexer and content of the
  the snippet. Example::


    {
        "url": "https://dpaste.de/xsWd",
        "lexer": "python",
        "conent": "The text body of the snippet."
    }

.. hint:: You need to adjust the setting ``DPASTE_BASE_URL`` which is used
    to generate the full qualified URL in the API response. See :doc:`settings`.

.. note:: When creating new snippets via the API, they won't be listed on the
    history page since they are related to a browser session.

.. _lexer list: https://github.com/bartTC/dpaste/blob/master/dpaste/highlight.py#L25

Example code snippets:
======================

A sample Python 2 script to publish snippets::

    #!/usr/bin/env python

    import urllib
    import urllib2
    import sys

    def paste_code():
        request = urllib2.Request(
            'https://dpaste.de/api/',
            urllib.urlencode([('content', sys.stdin.read())]),
        )
        response = urllib2.urlopen(request)
        # Strip surrounding quotes (NB: response has no trailing newline)
        print response.read()[1:-1]

    if __name__ == '__main__':
        paste_code()

You can simply use curl to publish a whole file::

    $ alias dpaste="curl -F 'content=<-' https://dpaste.de/api/?format=url"
    $ cat foo.txt | dpaste
    https://dpaste.de/ke2pB
