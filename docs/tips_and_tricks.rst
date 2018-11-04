Tips & Tricks
=============

Htop
----

To see the currently running processes, RAM usage and processor load, you can use the ``htop`` command.

While htop is running, you can press:

* ``h`` to see help
* ``H`` to toggle the display of user process threads
* ``q`` to quit

You can ran htop monochrome mode by lounching it with ``htop -C`` (I noticed some problems when running htop inside tmux, and monochrome mode helped).

SSH keys
--------

You can use SSH key to log in to Calcus without password authentication. See the excellent and very detailed explanation on `Arch Linux wiki <https://wiki.archlinux.org/index.php/SSH_keys>`_ or a shorter write-up in `Ubuntu documentation <https://help.ubuntu.com/community/SSH/OpenSSH/Keys>`_.

Bash keyboard shortcuts
-----------------------

Bash supports a `multitude of shortcuts <https://ss64.com/bash/syntax-keyboard.html>`_. Below are some which I find very useful.

``X + x`` mean that two keys have to be pressed simultaneously, while ``X-x`` means that they can be pressed sequentially.

* ``ESC-.`` or ``Alt + .`` last argument of the previous command
* ``Ctrl + r`` search history for the last command including the specified characters (searches as you type)
* ``Ctrl + a`` / ``Ctrl + e`` go to beginning / end of line (useful when Home / End don't work)
