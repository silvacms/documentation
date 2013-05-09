Maintaining Code Sources
========================

This document explains how to deal with Code Sources on the file system level.
Code Sources are usually first created in the ZMI and then dumped to the file
system. This allows their code to be checked into a source code control system
and simplifies future maintenance of Code Sources within Silva sites.

A Manager role is needed to create a Code Source in the ZMI. This process is
explained in the user documentation:
http://silvacms.org/docs/user/manager/creating_a_code_source

Sometimes a newly created Code Source is a one-off, meaning it won't be used
elsewhere. However if the Code Source needs to be maintained and/or used in
multiple sites then it makes sense to manage the Code Source via Silva's
Code Source Service. FMI see:
http://silvacms.org/docs/user/manager/code_source_service

A Silva developer can create a product that will contain all the Code Sources
used in various sites. These sources will then be available in the Code Source
Service.

Migrating a Code Source to the file system
------------------------------------------

Once a Code Source is created and tested in the ZMI, its code can be dumped to
the file system. In the Edit screen of a Code Sourse there is a "Dump to file
system" button and an "Export as ZIP" button. If you're working locally,
meaning you created the Code Source in a Silva running on your machine, then
the "Dump to file system" button dump all the files of the Code Source into a
directory with the same id. XXX is this correct?
If you're working on a server then the "Export as ZIP" function may be easier.
This will export Code Source the files packed in a ZIP file. Save the ZIP to
your disk and expand it.

Depending on the Code Source contents, there will be three or more files. As an
example we'll dump the TOC Code Source which will contain:

icon.png  parameters.xml  source.ini  toc.pt  toc_sort_on.py

* The icon which is used in the SMI.

* The paraemters XML file which contains the parameters form.

* The source.ini contains configuration, the same as in the Edit screen form.

* The toc.pt is the renderer, which can be a page template or Python script.

* A helper script, toc_sort_on.py, is also present.

The directory containing these items should be moved into the 'codesources'
directory of your product. It makes sense to give the directory the same name
as the id in the source.ini.

If you give the name a prefix this will cause the sources to be grouped
together in the ZMI, e.g. Silva's default Code Sources all have an id that is
prefixed with 'cs_'.
