SVN Merge
=========

This is a very basic tutorial that will demonstrate how to do an ``svn
merge``. In so doing it will go over many other common, and not so
common svn commands such as:

- svn add

- svn ci (checkin)

- svn co (checkout)

- svn cp (copy)

- svn delete

- svn diff

- svn import

- svn info

- svn log, svn log --stop-on-copy

- svn mkdir (make directory)

- svn resolved

In order to better illustrate the need for a merge, we will also
create a 'conflict'.

Finally and this is obvious, you'll need to have access to an svn
repository.

The first thing to do is create a 'test' environment in your svn
repository.::

  svn mkdir https://svn.your-svn.com/test/

Now make a directory called 'CoolApp' and add the file test.txt
with the contents 'foo'.

Pretend that CoolApp represents a large body of application code.

Go into directory and do ``svn import
https://svn.your-svn.com/test/CoolApp/trunk``

If you like go to you svn repository and check out your new
application!

A trunk is the main body of code for the application which is
developed upon.

Now make the 'tag' and 'branch' repositories. But before we do that
lets explain the concept of a tag and a branch. Lets start with the
more difficult one a branch.

A branch is a copy of the code base used to do two things; either
experiment and try out new stuff that may (will) break the trunk, or a
branch is a version of the code that shouldn't be developed on but
used to backport bugs from the trunk.

Experimentation branches should always be ``svn deleted`` after the
experiment either failed or has been merged into the trunk.

A tag is a stable version of the trunk or a branch that is released to
the public. Tags should never be modified after being made.

To make things more complicated, the above definitions are *not*
written law and can/will change from user to user. But this is how we
do it.

Now that the trunk is made lets make the branch and tag
repositories. Keep in mind we are not adding any branches or tags yet,
just making the space available.

In the CoolApp do::

  svn mkdir https://svn.your-svn.com/test/CoolApp/tag
  https://svn.your.com/test/CoolApp/branch

After you've done that lets make a real experimentation branch of the
software. Before proceeding try to follow this rule of thumb: NEVER
MAKE A BRANCH! The more branch of your software that are lying around
the more code you have to maintain, and more confusing things
become. So if you don't have to don't, but try not ever doing it!

When making an experimentation branch it is good practice to
follow a naming convention that will differentiate the 'bug branches'
from peoples experiments and vice versa. This is one
proposed name convention::

  experimentation branch        bug branch
  <maker-name>-<explicit-name>  <software-name>-<version-number>
  ted-mac-new-fields-test       SilvaForum-0.3.1

Lets return to the tutorial. Leave the CoolApp directory and checkout
the CoolApp trunk::

  svn co https://svn.your-svn.com/test/CoolApp/trunk CoolApp2

This will checkout the trunk into the CoolApp2 directory. Go into that
directory and do ``svn info``. This command will give you details
about the checkout.

Keep in mind that CoolApp where we did the ``svn import`` is not a
checkout of the trunk. If you do ``svn info`` in that directory it
will say "svn: '.' is not a working copy"

At this point lets make a branch. For this example this is an
experiment so keep in mind the name convention.::

  svn cp https://svn.your-svn.com/test/CoolApp/trunk
  https://svn.your-svn.com/test/CoolApp/branch/maker-name-the-experiment

When prompt to write the svn log, it is important at to write a very
good log entry such as: 'Branching to test the experiment to do foo'

Leave the CoolApp2 directory and checkout your new branch::

  svn co
  https://svn.your-svn.com/test/CoolApp/branch/maker-name-the-experiment
  maker-name-the-experiment

Enter the maker-name-the-experiment directory.

Lets make this checkout different from the trunk, and pretend what we
are adding amounts to a code experiment. Add a file the-experiment.txt
with the contents 'experiment'.

Pretend that the experiment text, had cause to alter the test.txt
code. Add 'bar' after the word foo in the test.txt file. Save and exit
the file.

Now do ``svn st``. You should see the following::

  ?    the-experiment.txt
  M    test.txt

The '?' means that svn doesn't know what this file is. Do ``svn add
the-experiment.txt``. Do ``svn st`` again and you should see::

  A    the-experiment.txt
  M    test.txt

As you've seen there are a number of different letters and symbols
used to denote the status of a file under svn control. The following
is a list of status::

  A Added
  C Conflict
  G Merged
  M Modified
  ? Unknown

Before checking in do ``svn info`` to make sure you are in the branch
copy. Then do ``svn diff`` to make sure your adding what you want to
add to the branch.

Now commit the changes ``svn ci -m 'adding an experiment'``.

Like in most active software projects while you are mucking about with
an experiment life continues on the trunk. To illustrate this return
to your trunk checkout of the CoolApp in CoolApp2.

Modify the test.txt file adding 'bee' after the word foo. By this time
you should be seeing a problem. The branch copy of test.txt has: 'foo
bar' in the contents, but the trunk test.txt has: 'foo bee'.

After doing ``svn st`` and ``svn diff``, checkin the change. ``svn ci
-m 'modify test.txt'``.

Looks like a perfect time to merge. Return to your branch copy of the
software and do ``svn log --stop-on-copy``. Note the version number,
which will be preceded by an 'r'.

``svn log --stop-on-copy`` will show the svn log where the trunk
received an ``svn cp`` command, so you should only do this command on
a branch.

Return to your trunk version and lets prepare to do the ``svn merge``
procedure. ``svn merge`` is like ``svn diff`` but it saves the changes
to your harddrive.

Doing ``svn merge`` in the trunk will apply the changes in the branch
to the trunk.

Okay do::

  svn merge -r #####:HEAD
  https://svn.your-svn.com/test/CoolApp/branch/maker-name-the-experiment/

Replace '#####' with the version number without the 'r'.

Since the test.txt files are the same but different svn will notify
you by saying: "Conflict discovered in 'test.txt'", and giving a list
of options you can choose from. Do ``df`` to see the conflict then do
``p``.

Do ``svn st`` and before test.txt you should see 'C' for conflict.

First you'll notice there are other 'new' files present in the
directory: a 'test.txt.merge-left.r#####', a
'test.txt.merge-right.r#####', and a 'test.txt.working' file. These
files show the difference per version. If you open the 'test.txt' file
it will have the same contents as the 'test.txt.working' file and the
contents of the 'test.txt.merge-right.r#####'. The 'working' is the
trunk contents and '.merge-right.r#####' is the branch contents. The
test.text.merge-left.r##### file is the contents of test.txt at the
point of the ``svn cp``.

In the test.txt file you will see the following::

  <<<<<<< .working
  foo bee
  =======
  foo bar
  >>>>>>> .merge-right.r#####

To merge look at the two sections and choose or edit the file
accordingly. Make sure to remove the conflict markers as you do
this.::

  foo bee bar

When everything is editted the way you want run ``svn resolved
test.txt``, do svn diff and make sure all the changes are the ones you
want.

If this kind of merging is happening for CMF software this is a good
time to run the test. If all runs well start the cmf instance and
checkout the software through the interface.

When you are satisfied check in the changes: ``svn ci -m 'resolved
conflict, merging``.

And delete the branch::

  svn delete
  https://svn.your-svn.com/test/CoolApp/branch/maker-name-the-experiment/
