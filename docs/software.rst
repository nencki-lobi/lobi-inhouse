Software
===========

Here you can find the list of currently available software packages and some specific manuals if relevant.

List
--------------------

| ants (neurodebian)
| freesurfer 6.0 (BIDS-Apps)
| docker (official repo)
| singularity (neurodebian)
| matlab + matlab-support dep: libxt6
| python 2.7
| python 3.6
| fsl 6.0.1 (default)
| fsl 5.0 build 509 (type fsl5.0-)
| dti-tk dep: libgl1
| mrtrix3 dep: git g++ python python-numpy libeigen3-dev zlib1g-dev libqt4-opengl-dev libgl1-mesa-dev libfftw3-dev libtiff5-dev (neurodebian)

Freesurfer
--------------------
Freesurfer is available as a Singularity container (/opt/software/bids_freesurfer.simg). All You need is to prepare Your data according to BIDS standard and run a long command eg. ``singularity run --bind /home/myname/data:/bids_dataset:ro,/home/myname/outputs:/outputs,/home/myname/freesurfer_license.txt:/license.txt /opt/software/bids_freesurfer.simg /bids_dataset /outputs participant --participant_label 01 --license_file "/license.txt"``, where license.txt is a file obtained after the FS's `registration <https://surfer.nmr.mgh.harvard.edu/registration.html>`. Other container specific options are listed on a `github <https://github.com/BIDS-Apps/freesurfer>`. Read more about Singularity containers `here <https://lobi-inhouse.readthedocs.io/en/latest/singularity.html>`.

ANTs
--------------------

..  caution:: Default version of ANTs is installed from neurodebian repository and it is quite outdated. It is recommended to use the newest ANTs inside Singularity container. Just change directory to ``/opt/software`` and type ``./ants.simg``.

Parallelization and multithreading
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ANTs enables parallel computation on 2 different levels - parallel execution of ANTs scripts and multithreaded executables of ITK library. Additionally, you can easily parallelize your scripts with GNU parallel [1]_ on top of it (eg. in case of multiple subjects). Make sure whether it is still true for current ANTs implementation. Here is the list of available options:

* bash global ``ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS`` (default: 8)
* buildtemplateparallel parameters ``-j`` & ``-c`` (paralell execution of max 2 scripts with j=2 & c=2)
* GNU parallel

It is highly advisable not to exceed 16 (physical) or 32 (hyperthreaded) number of available cores on Calcus. For example you can run 2 parallel jobs in buildtemplateparallel.sh with ``-j 2 -c 2`` with default ITK_GLOBAL...=8. The script will be executed on 8-16 cores depending on how many ANTs were prepared by btp.sh at the moment. Optionally, one can parallelize 2 subjects on top of it with GNU parallel resulting in 16-32 cores occupation.

.. [1] O. Tange (2018): GNU Parallel 2018, March 2018, https://doi.org/10.5281/zenodo.1146014.

.. _software-python:

Python
--------------------

Both python 2.7 and 3.6 are installed. Examples below assume that you are using python 3. Currently, pip has been installed only for python 3, but if you need it for python 2, just talk to Bartek or Michał.

Package management
^^^^^^^^^^^^^^^^^^

We are not installing any packages globally, but you are free to perform *user installs* [2]_ by running::

  pip3 install --user SomePackage

Virtual environments
^^^^^^^^^^^^^^^^^^^^

Additionally, you should consider using *virtual environments* for separate projects. For more details and explanations, see the Hitchhiker's guide to Python [3]_. Short outline is presented below.

First, install virtualenv::

  pip3 install --user virtualenv

Then, you can create the virtual environment for a project::

  cd my_project folder
  virtualenv venv

Activate it::

  source venv/bin/activate

And, when you are done, deactivate::

  deactivate


.. [2] Pip User Guide, https://pip.readthedocs.io/en/latest/user_guide/#user-installs
.. [3] Pipenv & Virtual Environments -- The Hitchhiker's Guide to Python, https://docs.python-guide.org/dev/virtualenvs/#lower-level-virtualenv
