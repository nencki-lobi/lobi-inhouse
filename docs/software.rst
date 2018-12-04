Software
===========

Here you can find the list of currently available software packages and some specific manuals if relevant.

List
--------------------

| ants (neurodebian)
| docker (official repo)
| singularity (neurodebian)
| matlab + matlab-support + libxt6
| python 2.7
| python 3.6
| fsl (fslinstaller.py)
| dti-tk + libgl1

ANTs
--------------------
Parallelization and multithreading
ANTs enables parallel computation on 2 different levels - parallel execution of ANTs scripts and multithreaded executables of ITK library. Additionaly, you can easily paralelize Your scripts with GNU parallel* on top of it (eg. in case of multiple subjects). Make sure whether it is still true for current ANTs implementation. Here is the list of available options:

|bash global ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS (default: 8)
|buildtemplateparallel parameters -j & -c (paralell execution of 2 scripts with j=2 & c=2)
|GNU parallel

It is highly advisable not to exceed 16 (physical) or 32 (hyperthreaded) number of available cores on Calcus. For example You can run 2 parallel jobs in buildtemplateparallel.sh with -j 2 -c 2 with default ITK_GLOBAL...=8. The script will be executed on 8-16 cores depending on how many subjobs are to be run by btp.sh at the moment. Optionally, one can parallel 2 subjects on top of it with GNU parallel.

* O. Tange (2018): GNU Parallel 2018, March 2018, https://doi.org/10.5281/zenodo.1146014.
