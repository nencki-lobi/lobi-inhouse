The command line: bash & SSH
============================

Since Calcus does not have a graphical interface, you will have to interact with it using bash.

Bash (the command line interface)
---------------------------------

If you never worked with bash, you will find a good introduction on the reputable `Software Carpentry <http://swcarpentry.github.io/shell-novice/>`_ website (don't worry, going through topics 1-3 should be enough to start working with Calcus).

Getting Bash
------------

If you use Linux or MacOS, bash is already installed on your system. All you have to do is launch the Terminal application.

If you are using Windows, you have several options available. The one I would recommend is to use `Windows Subsystem for Linux <https://docs.microsoft.com/pl-pl/windows/wsl/about>`_.

  The Windows Subsystem for Linux lets developers run GNU/Linux environment
  -- including most command-line tools, utilities, and applications --
  directly on Windows, unmodified, without the overhead of a virtual machine.

You can follow the guide linked above to install the subsystem. It boils down to these three steps:

1. Enable the Windows Subsystem for Linux feature.
   
   .. tip:: Instead of using PowerShell (as suggested in Microsoft's guide), you can follow these steps (in Polish): Panel Sterowania -> Programy i funkcje -> Włącz lub wyłącz funkcje systemu Windows -> Podsystem Windows dla systemu Linux.
2. Go to the Windows Store, install a Linux distribution (I would suggest Ubuntu and Debian) and initialize it according to the guide.
3. Initialize the chosen linux distribution (launch and complete the setup).

SSH
---

Bash offers you the ``ssh`` command, which allows you to log into a remote machine and execute commands. So after running::

  ssh username@remoteaddress

and logging in, you are working remotely. Type ``logout`` or ``exit`` to disconnect.

Please note that there are SSH clients which do not require you to have bash on your local computer (e.g. PuTTY), but I would recommend running bash even on Windows.
