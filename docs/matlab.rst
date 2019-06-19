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


Things to keep in mind
----------------------

Licensing
^^^^^^^^^

Licenses (activations) are counted per user per machine. This means that if you launch multiple instances of Matlab on one computer, you are using only one activation. However, if you have matlab running on your workstation and on Calcus, you are consuming two activations. If multiple users are running Matlab on Calcus, each of them consumes one activation. Please be mindful of others.

CPU usage
^^^^^^^^^

By default, Matlab will make use of all available CPUs. You can limit it to a single CPU by running it with the ``-singleCompThread`` option.
