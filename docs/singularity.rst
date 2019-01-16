Singularity
===========

Singularity is a container technology developed specifically for use on multi-tenant systems. A container allows you to run your software (or scientific workflow) in separation from the server's environment. You can use Singularity in a very similar way you would use Docker.

.. todo ::
  Everything related to fmriprep & bids apps needs to be rewritten and Neurodocker part needs to be written. The basics seem fine.

The basics
----------

Building containers
^^^^^^^^^^^^^^^^^^^

There are several ways to build a container [1]_. This can be done from Singularity Hub::

  singularity build lolcow.simg shub://GodloveD/lolcow

as well as from Docker Hub::

  singularity build lolcow.simg docker://godlovedc/lolcow

or from a recipe file (typically named `Singularity`) which you prepared or generated yourself::

  sudo singularity build lolcow.simg Singularity

The last one requires superuser privileges, so you can either build the image yourself on your personal computer and copy it to the server or ask an admin to build an image for you. Singularity runs natively on linux, but can be installed via `Vagrant <https://www.vagrantup.com/>`_ on other systems [2]_.

We might keep some popular container images in ``/opt/software`` on Calcus.

Running containers
^^^^^^^^^^^^^^^^^^

The containers are executable, so you can simply do::

  ./mycontainer.img [arguments]

Or you can launch them through singularity::

  singularity run mycontainer.img [arguments]

Binding folders
^^^^^^^^^^^^^^^

Binding [3]_ (mounting) is important if you want your container to interact with your files. ``$HOME``, ``/tmp``, ``/proc``, ``/sys`` and ``/dev`` are mounted by default.

There are two good ways to bind some folders. You can set the ``SINGULARITY_BINDPATH`` environment variable. This seems to be the most convenient method::

  export SINGULARITY_BINDPATH=/path/to/my/data:/data:ro,/path/to/my/output:/out

Alternatively, you can use the ``--bind`` option with ``singularity run``::

  singularity run --bind /path/to/my/data:/data:ro,/path/to/my/output:/out ./some.img [arguments & options]

In both cases, you should specify the bind paths as a comma-delimited string using the format ``src[:dest[:opts]]``. Possible options are ``ro`` (read only - used above for source data folder) and ``rw`` (read/write, default, used above for output directory).

The destination path must exist within the container! In BIDS apps you will often use ``/data`` and ``/out`` folders as in the examples above. Often, ``/mnt`` folder will be a good target. This restriction can be changed by using Overlay, but currently we stuck with the defaults and kept it disabled.

Use case: BIDS Apps
-------------------

You can use singularity to run `BIDS Apps <http://bids-apps.neuroimaging.io/about/>`_.

In most cases you should be able to build a singularity image directly from Docker Hub. For example::

  singularity build mriqc.img docker://poldracklab/mriqc:latest

Replace latest with the desired version (using the "latest" tag is `discouraged <https://vsupalov.com/docker-latest-tag/>`_).

In short::

  export SINGULARITY_BINDPATH=/opt/ssd/mszczepanik/my_dataset:/data:ro,/opt/ssd/mszczepanik/my_qc_output:/out
  ./mriqc.img [options] /data /out participant

FMRIPREP - Caveats
------------------

Tested using singularity v2.5.2 and fmriprep v1.2.1.

ANTS & number of threads
^^^^^^^^^^^^^^^^^^^^^^^^

It appears that environment variables defined outside the container are visible inside it, and the ``ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS`` (defines the number of threads used by ANTS, currently set to 12 on Calcus) seems to take precedence over the ``--nthreads`` and ``--omp-nthreads`` options of fmriprep. It is therefore recommended that you unset it before running fmriprep (and probably also mriqc)::

  unset ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS

Freesurfer license
^^^^^^^^^^^^^^^^^^

You need a freesurfer license to run fmriprep (even with ``--fs-no-reconall``). It has to be mounted inside the container and specified with ``--fs-license-file``. Because ``$HOME`` is mounted by default, you can put the license file in your home folder for simplicity.

Fmriprep example
^^^^^^^^^^^^^^^^

This example worked well (singularity image and freesurfer license were both in my home folder)::

  export SINGULARITY_BINDPATH=/opt/ssd/mszczepanik/movrest_sourcedata:/data:ro,/opt/ssd/mszczepanik/movrest_PREP:/out,/opt/ssd/mszczepanik/fmriprep_work2:/work
  unset ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS

  ./fmriprep.img --participant_label sub-12 \
                 --nthreads 4 \
                 --omp-nthreads 2 \
                 --mem_mb 16000 \
                 --fs-license-file license.txt \
                 --fs-no-reconall \
                 -w /work \
                 /data /out participant

Use case: Neurodocker
---------------------

References
----------

Singularity documentation

.. [1] `Build a container <https://www.sylabs.io/guides/2.6/user-guide/build_a_container.html>`_ - Singularity 2.6 docs
.. [2] `Installation <https://www.sylabs.io/guides/2.6/user-guide/installation.html>`_ - Singularity 2.6 docs
.. [3] `Bind Paths and Mounts <https://www.sylabs.io/guides/2.6/user-guide/bind_paths_and_mounts.html>`_ - Singularity 2.6 docs

Bids Apps on readthedocs

  * `Running mriqc <https://mriqc.readthedocs.io/en/stable/running.html>`_
  * `Fmriprep usage <https://fmriprep.readthedocs.io/en/stable/usage.html>`_
