Singularity
===========

Singularity is a container technology developed specifically for use on multi-tenant systems. You can use Singularity in a very similar way you would use docker. Among others, you can use it to run `BIDS Apps <http://bids-apps.neuroimaging.io/about/>`_.

Building from docker
--------------------

In most cases you should be able to build a singularity image directly from Docker Hub. For example::

  singularity build mriqc.img docker://poldracklab/mriqc:latest

Replace latest with the desired version (using the "latest" tag is `discouraged <https://vsupalov.com/docker-latest-tag/>`_).

Running Singularity images
--------------------------

You can tell Singularity to mount your data folders by using the ``SINGULARITY_BINDPATH`` environment variable::

    export SINGULARITY_BINDPATH=/opt/ssd/mszczepanik/my_dataset:/data:ro,/opt/ssd/mszczepanik/my_qc_output:/out
    
After that you can use the fact that ``.img`` files created with ``singularity build`` are executable and run QC in the following way::

  ./mriqc.img [options] /data /out participant
  
Alternatively, instead of setting ``SINGULARITY_BINDPATH`` variable you can use the ``--bind`` option with ``singularity run``::

  singularity run --bind /opt/ssd/mszczepanik/my_dataset:/data:ro,/opt/ssd/mszczepanik/qcout_test:/out ./mriqc.img [options] /data /out /participant

In both cases, you should specify the bind paths as a comma-delimited string using the format ``src[:dest[:opts]]``. Possible options are ``ro`` (read only - used above for source data folder) and ``rw`` (read/write, default, used above for output directory).

Singularity mounts folders ``$HOME``, ``/tmp``, ``/proc``, ``/sys`` and ``/dev`` by default.

Caveats
-------

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

References
----------

* Singularity documentation
  
  * `Singularity and Docker <https://www.sylabs.io/guides/2.6/user-guide/singularity_and_docker.html>`_
  * `Bind paths <https://www.sylabs.io/guides/2.6/user-guide/bind_paths_and_mounts.html>`_
    
* Bids Apps on readthedocs
  
  * `Running mriqc <https://mriqc.readthedocs.io/en/stable/running.html>`_
  * `Fmriprep usage <https://fmriprep.readthedocs.io/en/stable/usage.html>`_
