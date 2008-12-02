Troubleshooting while using buildout
====================================

If your cannot solve your problem, please report a bug on
https://bugs.launchpad.net/silva.

SVN 1.5 and buildout
--------------------

Under Windows or Unix, with SVN 1.5:

.. code-block:: sh

  unrecognized .svn/entries format; skipping .
  Traceback (most recent call last):
    File "c:\docume~1\arthur\locals~1\temp\tmpnmclvm", line 11, in ?
      execfile('C:\\silva-trunk\\src/five.grok\\setup.py')
    File "C:\silva-trunk\src/five.grok\setup.py", line 38, in ?
      entry_points="""
    File "C:\Python24\lib\distutils\core.py", line 149, in setup
      dist.run_commands()
    File "C:\Python24\lib\distutils\dist.py", line 946, in run_commands
      self.run_command(cmd)
    File "C:\Python24\lib\distutils\dist.py", line 966, in run_command
      cmd_obj.run()
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\d
  evelop.py", line 27, in run
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\d
  evelop.py", line 85, in install_for_development
    File "C:\Python24\lib\distutils\cmd.py", line 333, in run_command
      self.distribution.run_command(command)
    File "C:\Python24\lib\distutils\dist.py", line 966, in run_command
      cmd_obj.run()
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\e
  gg_info.py", line 171, in run
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\e
  gg_info.py", line 252, in find_sources
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\e
  gg_info.py", line 306, in run
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\e
  gg_info.py", line 333, in add_defaults
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\s
  dist.py", line 45, in walk_revctrl
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\s
  dist.py", line 52, in _default_revctrl
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\s
  dist.py", line 98, in entries_finder
  NameError: global name 'log' is not defined
  While:
    Installing.
    Processing develop directory 'C:\\silva-trunk\\src/five.grok'.

  An internal error occured due to a bug in either zc.buildout or in a
  recipe being used:
  Traceback (most recent call last):
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 1477, in main
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 324, in install
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 556, in _develop
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\easy_install.py", line 866, in develop
  AssertionError

You have to fix it by hand. In a shell, do:

.. code-block:: sh

   C:\Silva-trunk>cd eggs
   C:\Silva-trunk\eggs>move setuptools-0.6c8-py2.4.egg setuptools-0.6c8-py2.4.egg.zip

After with the explorer, unpack the archive
``setuptools-0.6c8-py2.4.egg`` in that same folder to the same
folder. Delete the archive after. Edit the file
``setuptools-0.6c8-py2.4.egg\setuptools\commands\sdist.py``, add a
line at the top of it:

.. code-block:: python

   from distutils import log

Rerun buildout, and thanks setuptools' maintainers for a good test
coverage of their software.
