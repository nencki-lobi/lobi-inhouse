Matlab
======

How to use
----------

There is no GUI. Type ``matlab`` to open command line interface. Feel free to run your scripts.

We recommend using your workstation for development and using Calcus for computation. In other words, on Calcus you should use matlab to run scripts rather than for interactive work.

.. todo::
   * Add more details and an example launch command;
   * Discuss SPM (separate subsection?)

Multivariate and Repeated Measures for Neuroimaging (MRM)
---------------------------------------------------------

MRM is pretty RAM demanding. It can be very efficient to estimate your model on Calcus comparing to standard PC. It goes like this:

1. Prepare your model in MRM GUI on Your PC. Save the model to MRM.mat. Upload both data and MRM.mat to Calcus.

2. Start MATLAB and add MRM toolbox::

    addpath /opt/software/MRM_v1.1

3. Adapt filepaths in the config file::

    spm_changepath('MRM.mat','C:/MyOldPath','\home\user\data')

  the output should be::

    \home\user\datafile1.nii
    \home\user\datafile2.nii
    ...
    \home\user\datafileN.nii
    \home\user\output
    => Fixing MRM.mat

4. Load and estimate model::

    load('MRM.mat')
    MRM_estimate

Matlab & GNU Parallel
---------------------

We have several licenses for Matlab's Parallel Computing Toolbox and there are no reasons not to use it. Should you wish to simply launch multiple instances of Matlab instead, `GNU parallel <https://www.gnu.org/software/parallel/>`_ presents an alternative. An example use case might be as follows.

Let's assume that the analysis can be launched using a function ``process_one`` which takes subject label as the input. In this case, subsequent inputs (subject labels) are placed in a text file ``to_process.txt``, one subject per line (and ideally wrapped in quotes, eg. "sub-01", so that they will be treated as strings when placed into a Matlab command and parallel will handle escaping). Copying strings from a table in Matlab GUI is an easy way to do make such file. If using the matlabbatch from SPM or PsPM, you can create a "single-subject" batch & script (i. e. without looping over subjects within the script) and turn it into a function. Analysis can be launched with::

  parallel -a to_process.txt --jobs 4 'matlab -nodisplay -nosplash -singleCompThread -batch "process_one({})" > /dev/null'

For GNU Paralell, ``-a`` (short for ``--arg-file``) is used to specify input file, and ``--jobs`` specifies the number of jobs to run in parallel. Then follows the command to be run in parallel, where ``{}`` denotes input line (to be inserted from the input file).

In the matlab call, the following options were used:

  * ``-nodisplay``: do not display any X commands; the MATLAB desktop will not be started (won't be needed from R2019 onwards)
  * ``-nosplash``: do not display the splash screen during startup (redundant if nodisplay is used?)
  * ``-singleCompThread``: limit each instance to one computing thread
  * ``-batch``: run the specified statement non-interactively (exits Matlab after the function ends; officially added in  R2019a but apparently works undocumented in R2018b)

Additionally:

  * The output was piped (``>``) to ``/dev/null`` (a black hole where everything sinks) - avoids cluttering the display, but makes it harder to see that something went wrong
  * Another Matlab option, ``-nojvm``, could be used in some cases to disable Java and speed up Matlab startup. Java is used mainly by GUI, but also for plotting, so this option was not used here.

Things to keep in mind
----------------------

Licensing
^^^^^^^^^

Licenses (activations) are counted per user per machine. This means that if you launch multiple instances of Matlab on one computer, you are using only one activation. However, if you have matlab running on your workstation and on Calcus, you are consuming two activations. If multiple users are running Matlab on Calcus, each of them consumes one activation. Please be mindful of others.

CPU usage
^^^^^^^^^

By default, Matlab will make use of all available CPUs. You can limit it to a single CPU by running it with the ``-singleCompThread`` option.
