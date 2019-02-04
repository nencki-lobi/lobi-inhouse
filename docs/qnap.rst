QNAP Network Attached Storage
=============================

Our Network Attached Storage (NAS) hardware is `QNAP TS-853U-RP <https://www.qnap.com/pl-pl/product/ts-853u-rp>`_ providing 18.7 TB of disk space on 8 drives configured in RAID-6 (meaning that two of them may fail with no data loss). It has 4 GB RAM and a 4-core processor.

The (local) IP address is ``192.168.199.46``.

User accounts
-------------

To use the server, you need to have a user account (with the exception of the Public folder, which can be accessed by everyone in the Institute network). Account creation only takes a moment, so talk to Michał about that.

How to access files
-------------------

Windows
^^^^^^^

Either hit start menu button and type ``\\192.168.199.46`` or enter the same in the file explorer window (note the double backslash at the beginning). You can create a shortcut for convenience. You will be prompted for your username and password, but these can be saved.

macOS
^^^^^

In Finder: in the "Go" menu click "Connect To Server" and type ``smb://192.168.199.46``. You will need to supply your user name and password. You can save the address and credentials.

Linux
^^^^^

This may vary depending on your distribution and desktop environment. If using GNOME, you can go to Files, then click "Other locations" and type ``smb://192.168.199.46``. You may be prompted to supply your user name and will have an option to save your credentials. Try using "SAMBA" or "WORKGROUP" when prompted for domain.

Web browser access
------------------

QNAP has a neat web browser interface. This can be accessed by typing ``192.168.199.46`` in web browser's address bar.

As a regular user you have only one app, File Station, where you can manage your files (although for everyday use you will probably prefer accessing them through your file browser). You can also change your password by clicking your username in the upper right corner and choosing "Options".

The administrator account has many more apps, including Control Panel, user management and shared folders settings.

Home folder and shared folders
------------------------------

Each user has his own *home folder*, visible only for them. Under ``home``, everybody sees their own folder (technically, all home folders are in the ``homes`` directory).

In addition, there is a possibility of creating *shared folders*. For each shared folder, there are access rights, which determine whether or not a given user can aceess the files. There are three possibilities: read/write, read only, no access. Only administrator can create shared folders and manage access rights, so talk to Michał if you need anything.

It is recommended that each user should keep their own data in their home folder and each research project should use a shared folder (accessible to those working on it) to enable collaboration.

The Public folder is *literally* public, which means people without user account (within the local network) have access too. This may be changed (admin: under shared folder settings) to allow registered users only. *Do not* keep sensitive information in the Public folder.

No limit on how much disk space a user can fill (*quota*) has been imposed. Since disk space is finite, please be considerate.

Qsync
-----
QNAP provides a `QSYNC <https://www.qnap.com/solution/smb-solution-qsync/pl-pl/>`_ file sync application for Windows, macOS and Ubuntu (beta). It should allow automatic synchronisation of selected folders (dropbox-style), including group sharing.

We have not tried it out yet, so if you do, please add to our documentation.

Other
-----

VPN
^^^

The QNAP is accessible only within the local network. When working remotely, you can connect using VPN to access all your files.

Recycle Bin
^^^^^^^^^^^

Delited files *should* be automatically moved to the network Recycle Bin. Retention time (admin: Control Panel - Kosz sieciowy) has been set to 180 days. We have *not* thoroughly verified the way recycle bin works, so please be careful.
