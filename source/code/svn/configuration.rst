.. _svn-configuration:

SVN Configuration
=================

SVN have a client-side configuration, located in your
``~/.subversion`` directory which might be interesting to tweak.

Configuration for Python development
------------------------------------

The configuration file ``~/.subversion/config`` is a Windows-INIT like
file, with sections.

By editing those can configure SVN to:

- Ignore ``.pyc``, ``.pyo`` and others files which should never be added
  in SVN. Add or edit the option ``global-ignore`` in the section
  ``[miscellany]``::

    global-ignores = *.o *.lo *.la #*# .*.rej *.rej .*~ *~ .#* .DS_Store *.pyc *.pyo *.egg-info

  This setting is recommended when using buildout.

- Set the ``svn:keywords`` correctly on Python files. For that, you
  need first to activate the default SVN properties in the section
  ``[miscellany]`` by setting ``enable-auto-props = yes``. After, you
  can add in the ``[auto-props]`` section::

    *.py = svn:keywords=Author Date Id Revision

- Configure you favorite SVN to use your favorite editor to write
  commit message. In the ``[helpers]`` section, edit the
  ``editor-cmd`` option::

    editor-cmd = vim


Configure SVN to work with HTTP proxies
---------------------------------------

You can create a ``~/.subversion/server`` file to configure that,
containing::

  [global]
  http-proxy-host = name-of-your-proxy
  http-proxy-port = 8080


