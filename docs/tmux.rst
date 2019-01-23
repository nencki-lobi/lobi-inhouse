Tmux
====

Tmux is a wonderfully useful tool. We recommend using it for long-running pipelines: you can connect to Calcus over SSH, start your script inside a tmux session, detach from tmux and logout - this way, your script will keep on running, even though you are logged out (typically when you disconnect from SSH, your running processes are terminated, but the tmux session remains active on its own). Furthermore, with its windows and panes, tmux comes useful when you want to do multiple things in one terminal window. Another program, GNU Screen, serves the same purpose, but is not covered by this manual.

Introduction
------------

For a concise, enthusiastic, 10 minutes long introduction to tmux watch `The Lunduke Show episode on tmux <https://www.youtube.com/watch?v=5iXzqN8-34E>`_.

Usage
-----

Outside tmux
^^^^^^^^^^^^

| ``tmux`` to start tmux
| ``tmux attach`` to attach to a previously started session
| ``tmux ls`` to list running sessions

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
