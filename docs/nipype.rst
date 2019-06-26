Nipype
======

Nipype is a python library for creating pipelines, able to combine multiple processing steps from different tools (SPM, FSL and others).
In a way it is similar to SPM batch & script interface.

Nipype can be useful not only as a tool for building elaborate pipelines, but also as a simple wrapper for executing single steps.
For example, in the walkthrough below, we use it to run spm smoothing for a group of subjects,
with all scripting done in python instead of matlab.

An `excellent tutorial <http://miykael.github.io/nipype-beginner-s-guide/index.html>`_ has been created for Nipype by Michael Notter.
There, the `first steps <http://miykael.github.io/nipype-beginner-s-guide/firstSteps.html>`_ chapter is especially valuable.
In this page, we provide a description of one practical use case to demonstrate the utility of nipype.
We are focusing on certain elements, without giving a methodic introduction to nodes and workflows.

Installation and configuration
------------------------------

Given that FSL, matlab and python are installed on calcus, nipype requires little preparation.

Since we decided not to install any python packages system-wide, you have to install nipype for yourself
(``pip install --user nipype``).
You should probably consider using a virtualenv (see our docs on :ref:`Python <software-python>` for details).

You should obtain SPM yourself and place it in a location of your choice, for example in ``~/spm12``.
Then you should add it to matlab path. Best way to do it is through a startup file [1]_.
If you don't have it yet, create a file ``~/matlab/startup.m`` and add the following::

  addpath /home/username/spm12

The fsl interface should work out of the box.

Use case: smoothing fmriprep derivatives with spm
-------------------------------------------------

Introduction
^^^^^^^^^^^^

Fmriprep [2]_ (which, by the way, is based on nipype) is an automatic preprocessing pipeline.
Given that smoothing the data is straightforward and, depending on planned analyses,
different levels of smoothing may be required, this step has been left out of fmriprep.
Furthermore, fmriprep produces compressed (``.nii.gz``) files, which need to be unpacked before putting them into spm.

So in this case, I want to take non-smoothed, preprocessed ``.nii.gz`` files and smooth them using spm.

My data structure
^^^^^^^^^^^^^^^^^

The fmriprep output directory is in::

  /opt/ssd/mszczepanik/emocon/derived/fmriprep

and inside, a path to a file looks like this::

  sub-Azcnxv/func/sub-Azcnxv_task-de_space-MNI152NLin2009cAsym_desc-preproc_bold.nii.gz

in the end, I want to put smoothed files in::

  /opt/ssd/mszczepanik/emocon/derived/spm/smooth

The walkthrough
^^^^^^^^^^^^^^^

First, required imports (I will explain them later)::

  import nipype.interfaces.spm as spm
  import os

  from nipype.interfaces.utility import IdentityInterface
  from nipype.interfaces.io import SelectFiles, DataSink
  from nipype.algorithms.misc import Gunzip
  from nipype.pipeline.engine import Workflow, Node

Then, I should define the paths to my data::

  DERIV_DIR = '/opt/ssd/mszczepanik/emocon/derived'
  WORK_DIR = '/opt/ssd/mszczepanik/emocon/work'

  func_template = os.path.join(
      'fmriprep',
      'sub-{subject_id}',
      'func',
      ('sub-{subject_id}_task-{task_label}_space-MNI152NLin2009cAsym_'
       'desc-preproc_bold.nii.gz'),
      )

  codes = ['Azcnxv', 'Bqhldk']
  task_names = ['ofl', 'de']

The ``func_template`` is a string containing the path to a file, relative to ``DERIV_DIR`` with subject ID and task label
replaced by expressions in curly braces: ``{subject_id}`` and ``{task_label}``.
These will be useful later, because we will have Nipype fill them in for us.
I also prepared lists of subject codes and task names to be used (for this example, I am processing two subjects only).

Now it's time to create pipeline *nodes*. We will create them first, and connect them later.
Let's start with gunzip to decompress files. This one is simple::

  gunzip = Node(Gunzip(), name='gunzip')

What's intresting, it will not unzip the files in place, but rather put unzipped copies of the files in a *working directory*,
so the source files will remain untouched.

Next in line is the smoothing node.
Full documentation, with a list of inputs and outputs, can be found `here <https://nipype.readthedocs.io/en/1.2.0/interfaces/generated/interfaces.spm/preprocess.html#smooth>`_.
We do not define the obligatory ``in_files`` input, because we are planning to feed multiple values into the pipeline later::

  smooth = Node(spm.Smooth(), name='smooth')
  smooth.inputs.fwhm = [8, 8, 8]
  smooth.inputs.out_prefix = 'sm8_'

With the two nodes in place, we should start worrying about input and output. First, let's create an ``IdentityNode``.
Its job is to pass values to other nodes. Let's name it *infosource* and give it two fields, through which we will
be specifying subjects and tasks. What's important is that we will change its fields into *iterables*,
meaning that they will accept lists of inputs and split the workflow into multiple copies (we do this because we want to
process multiple subjects and multiple tasks)::

  infosource = Node(
      IdentityInterface(fields=['subject_id', 'task_label']),
      name='infosource')
  infosource.iterables = [('subject_id', codes),
                          ('task_label', task_names)]


For selecting files, Nipype has ``SelectFiles`` node. Remember the ``func_template`` string above? We will use it now::

  templates = {'func': func_template}
  selectfiles = Node(
      SelectFiles(templates, base_directory=DERIV_DIR),
      name='selectfiles')

``SelectFiles`` can take more than one template, thus splitting the data into several logical categories
(e.g. anatomical and functional), but here we need just one. Keys of the templates dictionary are used to define output names.
The template defines path relative to ``base_directory``.

With input blocks ready, it's time for ``DataSink``, which is a node for writing output files::

  datasink = Node(DataSink(base_directory=DERIV_DIR, container='spm'),
                  name='datasink')

  # nodes add pre-/postfix to file or folder, change it
  datasink.inputs.substitutions  = [('_subject_id_', 'sub-')]
  datasink.inputs.regexp_substitutions = [('_task_label_[a-z]+', '')]

The ``container`` parameter specifies name of the folder to be placed in ``base_directory``. All outputs of the data sink
will be collected there. However, nodes create their own folder names, so I defined some substitutions to simplify output
paths. I used both ``substitutions`` (simple replacement) and ``regexp_substitutions`` (regular expression replacement);
if both are present, the simple ones are performed first. Best way to figure out what substitutions will be required
is to run the pipeline on a single subject.

With all nodes ready and waiting, it's time to create a workflow::

  wf = Workflow(name='smooth_wf')
  wf.base_dir = WORK_DIR

Workflow's ``base_dir`` is where all intermediate files will be stored. It can (and should) be deleted after workflow completes
successfully.

Finally, it's time to connect all nodes together. Let's do it in one sweep::

  wf.connect([
      (infosource, selectfiles, [('subject_id', 'subject_id'),
                                 ('task_label', 'task_label')]),
      (selectfiles, gunzip, [('func', 'in_file')]),
      (gunzip, smooth, [('out_file', 'in_files')]),
      (smooth, datasink, [('smoothed_files', 'smooth')])
      ])

Which means that we want to connect:

| ``infosource.subject_id`` → ``selectfiles.subject_id``
| ``infosource.task_label`` → ``selectfiles.task_label``
| ``selectfiles.func`` → ``gunzip.in_file``
| ``gunzip.out_file`` → ``smooth.in_files``
| ``smooth.smoothed_files`` → ``datasink.smooth``

Most of these connection points are defined by the nodes (e.g. ``spm.Smooth`` has one output, called ``smoothed_files``).
You can find them in Nipype's API. For some nodes we defined the names ourselves upon creation:
in ``infosource`` by specifying ``fields`` attribute, and in ``selectfiles`` by specifying templates (dictionary keys
define outputs, expressions in curly braces define inputs). The datasink is unique in that it does not need any inputs defined
in advance. Instead, the names we give when connecting will be translated to output folder names.
Use ``foo.bar`` to create subdirectories (``foo/bar``). Since one input cannot be connected to twice, use ``foo`` and ``foo.@bar``
to place two things in ``foo`` without creating subdirectories.

Finally, it's time to run the workflow. We can use parallelisation. Here, I'm using 4 processor cores::

  wf.run('MultiProc', plugin_args={'n_procs': 4})

Nipype runs matlab processes with ``-singleCompThread`` option. The example above will be spawning up to 4 such processes,
so that 4 files will be smoothed in parallel.

Output files are structured as follows::

  /opt/ssd/mszczepanik/emocon/derived/spm
  └── smooth
      ├── sub-Azcnxv
      │   ├── sm8_sub-Azcnxv_task-de_space-MNI152NLin2009cAsym_desc-preproc_bold.nii
      │   └── sm8_sub-Azcnxv_task-ofl_space-MNI152NLin2009cAsym_desc-preproc_bold.nii
      └── sub-Bqhldk
          └── sm8_sub-Bqhldk_task-de_space-MNI152NLin2009cAsym_desc-preproc_bold.nii
          └── sm8_sub-Bqhldk_task-ofl_space-MNI152NLin2009cAsym_desc-preproc_bold.nii


References
----------

.. [1] Matlab docs, https://uk.mathworks.com/help/matlab/ref/startup.html
.. [2] fmriprep documentation, https://fmriprep.readthedocs.io/en/stable/
