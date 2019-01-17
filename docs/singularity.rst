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

Example - MRIQC
^^^^^^^^^^^^^^^^^^

First, prepare your image (versions change frequently, so it's best if you do this yourself)::

  singularity build mriqc.simg docker://poldracklab/mriqc:latest

Replace "latest" with the desired version (using the "latest" tag is `discouraged <https://vsupalov.com/docker-latest-tag/>`_).
To find the tags and their release dates you can (for example) check `mriqc page on docker hub <https://hub.docker.com/r/poldracklab/mriqc/tags>`_.

Then you specify bindings for your data (read only) and output folders::

  export SINGULARITY_BINDPATH=/path/to/your_dataset:/data:ro,/path_to/your_qc_output:/out

and run QC like this::

  ./mriqc.simg [options] /data /out participant

For the list of options see [4]_

Example - FMRIPREP
^^^^^^^^^^^^^^^^^^

Tested using singularity v2.5.2 and fmriprep v1.2.1.

Caveat: ANTS & number of threads
""""""""""""""""""""""""""""""""

It appears that environment variables defined outside the container are visible inside it, and the ``ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS`` (defines the number of threads used by ANTS, currently set to 8 on Calcus) seems to take precedence over the ``--nthreads`` and ``--omp-nthreads`` options of fmriprep. It is therefore recommended that you unset it before running fmriprep (and probably also mriqc)::

  unset ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS

Caveat: freesurfer license
""""""""""""""""""""""""""

You need a freesurfer license to run fmriprep (even with ``--fs-no-reconall``). It has to be mounted inside the container and specified with ``--fs-license-file``. Because ``$HOME`` is mounted by default, you can put the license file in your home folder for simplicity.

Usage example
"""""""""""""

Taking the above into consideration, I used the following script::

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

See fmriprep docs [5]_ for the description of options.

Use case: Neurodocker
---------------------

Neurodocker [6]_ is a wonderful tool which can generate Docker and Singularity recipes for most common neuroimaging applications. You can run it using docker or install it using pip.

Creating recipes
^^^^^^^^^^^^^^^^

Let's assume that you want to work with ANTs version 2.3.1. You can create the recipe using docker::

  docker run --rm kaczmarj/neurodocker:0.4.3 generate singularity \
    --base debian:stretch --pkg-manager apt --ants version=2.3.1 \
    > Singularity

or, if you installed through pip::

  neurodocker generate singularity \
    --base=debian:stretch --pkg-manager=apt --ants version=2.3.1 \
    > Singularity

In both cases we redirected the output to a file named `Singularity`, so we can build::

  sudo singularity build ants231.simg Singularity

Note that both docker itself and building from recipe in singularity require administrator privileges, so either execute these steps on your computer and copy the results or ask an admin to run them directly on Calcus.

You can combine different packages in one container. See the description and examples on `Neurodocker's github <https://github.com/kaczmarj/neurodocker>`_ to see what you can do with it. And remember that if something is missing, you can use the generated recipes (they are just text files, try looking inside!) as a base and add your own stuff, e.g. by adding some ``apt install`` lines.

Using containers created with Neurodocker
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's continue with the previous example. Executing the image::

  ./ants231.simg

will put you inside the container (technically speaking, it will start bash inside the container). You will have access to the programs installed within, instead of those installed natively on Calcus. You can test that::

  which ANTS

Finally, you can finish working in the container and go back to the outer environment::

  exit

When inside the container, you will not be able to access your files unless mounted (explicitly or by default, see above for details). Since Overlay is not currently enabled, you will have to mount to an existing location; ``/mnt`` seems like a sound choice. Assuming you want to work with data kept in ``/opt/ssd/myfolder``, you can do::

  export SINGULARITY_BINDPATH=/opt/ssd/myfolder:/mnt
  ./ants231.simg

Getting a hang of inside vs outside
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once again, let's continue with the previous example. Assuming you are `outside` the container you can try the following::

  # see where system installed programs are
  which ANTS
  which tmux

  # make sure you are not binding anything beyond default
  unset SINGULARITY_BINDPATH

  # enter the container
  ./ants231.simg

  # you have different ANTS at your disposal
  which ANTS

  # container has just the basics, no tmux inside
  which tmux

  # home folder is mounted by default
  ls ~/

  # but not e.g. /opt/ssd
  ls /opt

  # go back outside
  exit


References
----------

Singularity documentation

.. [1] `Build a container <https://www.sylabs.io/guides/2.6/user-guide/build_a_container.html>`_ - Singularity 2.6 docs
.. [2] `Installation <https://www.sylabs.io/guides/2.6/user-guide/installation.html>`_ - Singularity 2.6 docs
.. [3] `Bind Paths and Mounts <https://www.sylabs.io/guides/2.6/user-guide/bind_paths_and_mounts.html>`_ - Singularity 2.6 docs

Bids Apps on readthedocs

.. [4] `Running mriqc <https://mriqc.readthedocs.io/en/stable/running.html>`_ - mriqc docs
.. [5] `Fmriprep usage <https://fmriprep.readthedocs.io/en/stable/usage.html>`_ - fmriprep docs

Neurodocker

.. [6] https://github.com/kaczmarj/neurodocker
