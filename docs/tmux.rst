Tmux
====

Tmux is a wonderfully useful tool. We recommend using it for long-running pipelines: you can connect to Calcus over SSH, start your script inside a tmux session, detach from tmux and logout - this way, your tasks will keep on running, even though you are logged out (typically when you disconnect from SSH, your running processes are terminated, but the tmux session remains active on its own).

Furthermore, with its windows and panes, tmux comes useful when you want to do multiple things in one terminal window. Another program, GNU Screen [1]_, serves the same purposes, and is also available on calcus, but is not covered by this manual.

Introduction
------------

For a concise, enthusiastic, 10 minutes long introduction to tmux watch `The Lunduke Show episode on tmux <https://odysee.com/@Lunduke:e/tmux-the-desktop-environment-for-your:a>`_ (highly recommended).

The very basics
---------------

Tmux is used to start *sessions* (collections of pseudo-terminals). A session has at least one *window*, and each window can be split into *panes*. Sessions are persistent against disconnection (be it intentional or accidental). When a session is running, you can detach from it and reattach back later (even after logging out and back in, or from another computer).

From an introduction to exhaustive details, you'll find everything in tmux's wiki [2]_ or in its man page [3]_. The most useful commands are listed below.

Usage
-----

Outside tmux
^^^^^^^^^^^^

Three most important commands:

| ``tmux`` to start tmux and attach to a new session (equivalent ``tmux new``)
| ``tmux attach`` to attach to a previously detached session
| ``tmux ls`` to list running sessions

More specific, also useful:

| ``tmux attach -t target-session`` to attach to a specific session
| ``tmux kill -t target-session`` to destroy given session


Inside tmux (key bindings)
^^^^^^^^^^^^^^^^^^^^^^^^^^

Tmux may be controlled from an attached client by using a key combination of a prefix key, ``C-b`` (Ctrl-b) by default, followed by a command key. Here are selected default key bindings (taken from the man page).

General

| ``C-b`` Send the prefix key (C-b) through to the application.
| ``C-b d`` Detach the current client.
| ``C-b ?`` List all key bindings.
| ``C-b :`` Enter the tmux command prompt.

Panes

| ``C-b "`` Split the current pane into two, top and bottom.
| ``C-b %`` Split the current pane into two, left and right.
| ``C-b o`` Rotate the panes in the current window forwards (i. e. move to the next pane).
| ``C-b x`` Kill the current pane.
| ``C-b [`` Enable scrolling around (with arrows or PgDn&PgUp). Press ``q`` to quit.


Windows

| ``C-b c`` Create a new window.
| ``C-b [0 to 9]`` Select windows 0 to 9.
| ``C-b &`` Kill the current window.
| ``C-b q`` Briefly display pane indexes.


References
----------

.. [1] Screen - GNU Project - Free Software Foundation https://www.gnu.org/software/screen/
.. [2] Getting Started - tmux wiki https://github.com/tmux/tmux/wiki/Getting-Started
.. [3] tmux - OpenBSD manual pages https://man.openbsd.org/tmux

