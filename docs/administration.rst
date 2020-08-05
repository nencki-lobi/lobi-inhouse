Admin actions
=============

Things that can / should be done by the administrator are kept here, so that we don't forget how to do them when they have to be done again.

Adding users
------------

Instead of the default ``adduser`` (and low-level ``useradd``) it is preferred to use the ``/root/scripts/add_user.sh`` script contributed by P. Redel.
In addition to wrapping around ``useradd`` it takes care of other things like setting up samba. Usage::

    ./add_user.sh USERNAME* - the password will be generated automatically
    ./add_user.sh USERNAME* PASSWORD - password in unencrypted form (avoid special characters)
    
For usernames, we try to keep the convention of using first letter of name followed by surname (eg. jsmith).

MOTD
----

Ubuntu uses the `update-motd <http://manpages.ubuntu.com/manpages/bionic/en/man5/update-motd.5.html>`_ framework to assemble the Message Of The Day (text that gets displayed upon login). In short: executable scripts are kept in ``/etc/update-motd.d/*``.

Of those files, ``50-motd-news`` is particularly notable. It uses a configuration file, ``/etc/default/motd-news``. From there, the news can be enabled/disabled (by setting ``ENABLED`` to 1 or 0), and their source can be configured. By default, it uses https://motd.ubuntu.com/, but other services can be added.

I added a login banner (``99-banner-lobi``) and disabled the display of the news.

Other scripts may be disabled by unsetting the ``-x`` permission.
