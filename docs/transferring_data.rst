Transferring data
=================

You have several ways of transferring your data to and from Calcus.

SCP
---

The simplest. For example, running the following on your local machine::
  
  scp myfile username@192.168.199.58:path/to/myfile_there

will copy ``myfile`` to ``~/path/to/myfile_there`` on Calcus. This means that if there is no slash after the colon, the path is interpreted as relative to your home directory. Of course, you can also use absolute paths::

  scp myfile username@192.168.199.58:/opt/ssd/myfolder/myfile_there

You can copy files from calcus by reversing the order of arguments.

You can copy folders by using ``-r`` (recursive) flag.

Rsync
-----

The more sophisticated and powerful option. Rsync verifies checksums while copying (so it should be the preferred method for data files) and is especially useful for copying (synchronising) folders. Two things to keep in mind::

  rsync -rv foo/bar username@192.168.199.58:data

will recursively transfer all files from your local ``bar`` directory into ``~/data/bar`` on Calcus, while adding a trailing slash on the source::

  rsync -rv foo/bar/ username@192.168.199.58:data

will change this behaviour to avoid creating an additional directory on the destination (i.e. will put the ``bar`` contents into ``~/data/``).

In both cases, ``-r`` means recursive, and ``-v`` means verbose. When copying large files, you may wish to add the ``--progress`` option to see how quickly files are flowing.

Again, you can swap argument order to copy from Calcus.


Samba
-----

Samba file transfer is available by default on most popular operating systems (Windows, OSx, Debian/Ubuntu). You can browse content of Your HOME folder easily. Just type in Your file browser:

* Windows - ``\\192.168.199.58\``
* OSx and Linux - smb://192.168.199.58

and log in with Your default credentials.

Collaborators without a SSH account on the server can also login by guest credentials (ask admin) and access data in /home/public. Go on and share Your results with Your co-workers!

