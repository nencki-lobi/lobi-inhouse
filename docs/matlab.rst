Matlab
======

How to use
----------

We recommend using your workstation for development and using Calcus for computation. In other words, on Calcus you should use matlab to run scripts rather than for interactive work.

.. todo::
   * Add more details and an example launch command;
   * Discuss SPM (separate subsection?)


Things to keep in mind
----------------------

Licensing
^^^^^^^^^

Licenses (activations) are counted per user per machine. This means that if you launch multiple instances of Matlab on one computer, you are using only one activation. However, if you have matlab running on your workstation and on Calcus, you are consuming two activations. If multiple users are running Matlab on Calcus, each of them consumes one activation. Please be mindful of others.

CPU usage
^^^^^^^^^

By default, Matlab will make use of all available CPUs. You can limit it to a single CPU by running it with the ``-singleCompThread`` option.

