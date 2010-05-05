%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
Installing Varnish on your computer
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

With open source software, you can choose to install binary
packages or compile stuff from source-code. 

In general, from a point of principle, I would argue that
everybody should compile from source, but realistically
binary packages are *so much easier* so lets cover that first:


Installing Varnish from packages
================================

Installing Varnish on most relevant operating systems can usually 
be done with with the systems package manager, typical examples
being:

FreeBSD (from source)
	``cd /usr/ports/varnish && make install clean``
FreeBSD (binary package)
	``pkg_add -r varnish``

If that worked for you, you can skip the rest of this document
for now, and and start reading the much more interesting :ref:`Tutorial`
instead.


Compiling Varnish from source
=============================

If there are no binary packages available for your system, or if you
want to compile Varnish from source for other reasons, follow these
steps:

First get a copy of the sourcecode using the ``svn`` command.  If
you do not have this command, you need to install SubVersion_ on
your system.  There is usually a binary package, try substituting
"subversion" for "varnish" in the examples above, it might just work.

To get the development source code::

	svn co http://varnish-cache.org/svn/varnish/trunk

or if you want the production branch::

	svn co http://varnish-cache.org/svn/varnish/branches/2.1

Next, configuration:  For this you will need ``libtoolize``, ``aclocal``,
``autoheader``, ``automake`` and ``autoconf``, also known as *the
autocrap tools* installed on your system.

Once you have them::

	cd varnish-cache
	sh autogen.sh
	sh configure
	make

The ``configure`` script takes some arguments, but more likely than
not, you can forget about that for now, almost everything in Varnish
are runtime parameters.

Before you install, you may want to run the regression tests, make
a cup of tea while it runs, it takes some minutes::

	(cd bin/varnishtest && ./varnishtest tests/*.vtc)

Don't worry of a single or two tests fail, some of the tests are a
bit too timing sensitive (Please tell us which so we can fix it) but
if a lot of them fails, and in particular if the ``b00000.vtc`` test 
fails, something is horribly wrong, and you will get nowhere without
figuring out what.

And finally, the true test of a brave heart::

	make install

.. _SubVersion: http://subversion.tigris.org/


Did you call them *autocrap* tools ?
====================================

Yes, in fact I did, because they are the worst possible non-solution
to a self-inflicted problem.

Back in the 1980'ies, the numerous mini- and micro-computer companies
all jumped on the UNIX band-wagon, because it gave them an operating
system for their hardware, but they also tried to "distinguish" themselves
from the competitors, by "adding value".

That "value" was incompatibility.

You never knew where they put stuff, what arguments the compiler needed
to behave sensibly, or for that matter, if there were a compiler to begin
with.

So some deranged imagination, came up with the idea of the ``configure``
script, which sniffed at your system and set up a ``Makefile`` that would
work.

Writing configure scripts was hard work, for one thing you needed a ton
of different systems to test them on, so copy&paste became the order of
the day.

Then some even more deranged imagination, came up with the idea of
writing a script for writing configure scripts, and in an amazing
and daring attempt at the "all time most deranged" crown, used an
obscure and insufferable macro-processor called ``m4`` for the
implementation.

Now, as it transpires, writing the specification for the configure
producing macros was tedious, so somebody wrote a tool to...

...do you detect the pattern here ?

Now, if the result of all this crap, was that I could write my source-code
and tell a tool where the files were, and not only assume, but actually
*trust* that things would just work out, then I could live with it.

But as it transpires, that is not the case.  For one thing, all the
autocrap tools add another layer of version-madness you need to get
right before you can even think about compiling the source code.

Second, it doesn't actually work, you still have to do the hard work
and figure out the right way to explain to the autocrap tools what
you are trying to do and how to do it, only you have to do so in 
a language which is used to produce M4 macro invocations etc. etc.

In the meantime, the UNIX diversity has shrunk from 50+ significantly
different dialects to just a handful: Linux, \*BSD, Solaris and AIX
and the autocrap tools have become part of the portability problem,
rather than part of the solution.

Amongst the silly activites of the autocrap generated configure script
in Varnish are:

* Looks for ANSI-C header files (show me a system later
  than 1995 without them ?)

* Existence and support for POSIX mandated symlinks, (which
  are not used by Varnish btw.)

* Tests, 19 different ways, that the compiler is not a relic from
  SYS III days.  (Find me just one SYS III running computer with
  an ethernet interface ?)

* Checks if the ISO-C and POSIX mandated ``cos()`` function exists
  in ``libm`` (No, I have no idea either...)

&c. &c. &c.

Some day when I have the time, I will rip out all the autocrap stuff
and replace it with a 5 line shellscript that calls ``uname -s``.

Poul-Henning, 2010-04-20