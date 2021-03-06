.. _installation:

Installation
************

Installing via `conda`
======================

The simplest way to install YANK is via the `conda <https://www.continuum.io/content/conda-data-science>`_  package
manager.
Packages are provided on the `omnia Anaconda Cloud channel <http://anaconda.org/omnia>`_ for Linux, OS X, and Win platforms.
The `yank Anaconda Cloud page <https://anaconda.org/omnia/yank>`_ has useful instructions and `download statistics <https://anaconda.org/omnia/yank/files>`_.

If you are using the `anaconda <https://www.continuum.io/downloads/>`_ scientific Python distribution, you already have the ``conda`` package manager installed.
If not, the quickest way to get started is to install the `miniconda <http://conda.pydata.org/miniconda.html>`_ distribution, a lightweight minimal installation of Anaconda Python.

On ``linux``, you can install the Python 3.6 version into ``$HOME/miniconda3`` with:

.. code-block:: bash

   $ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
   $ bash ./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
   $ export PATH="$HOME/miniconda3/bin:$PATH"

On ``osx``, you want to use the ``osx`` binary

.. code-block:: bash

   $ wget https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
   $ bash ./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
   $ export PATH="$HOME/miniconda3/bin:$PATH"

You may want to add the new ```$PATH`` extension to your ``~/.bashrc`` file to ensure Anaconda Python is used by
default.
Note that YANK will be installed into this local Python installation, so that you will not need to worry about
disrupting existing Python installations.

.. note::

   ``conda`` installation is the preferred method since all dependencies are automatically fetched and installed
   for you.

|

Release build
-------------

You can install the latest stable release build of YANK via the ``conda`` package with

.. code-block:: none

   $ conda config --add channels omnia --add channels conda-forge
   $ conda install yank

This version is recommended for all users not actively developing new algorithms for alchemical free energy calculations.

.. note:: ``conda`` will automatically dependencies from binary packages automatically, including difficult-to-install packages such as OpenMM, numpy, and scipy. This is really the easiest way to get started.

|

Development build
-----------------

The bleeding-edge, absolute latest, very likely unstable development build of YANK is available on GitHub commit,
and can be obtained by :ref:`installing from source <install_from_source>` (and installed into whatever the current
`conda environment <https://conda.io/docs/using/envs.html>`_ is):

.. warning:: Development builds may be unstable and are generally subjected to less testing than releases.  Use at your own risk!


Upgrading your installation
---------------------------

To update an earlier ``conda`` installation of YANK to the latest release version, you can use ``conda update``:

.. code-block:: bash

   $ conda update yank

|

.. _yank-dev-conda-package:

Testing your installation
-------------------------

Test your YANK installation to make sure everything is behaving properly on your machine:

.. code-block:: bash

   $ yank selftest

This will check that installation paths are correct and run a battery of tests that ensure any
automatically detected GPU hardware is behaving as expected. If installed, it will also check your OpenEye installation.

|

.. _yank-platforms:

Testing Available Platforms
---------------------------

You will want to make sure that all GPU accelerated platforms available on your hardware are accessible to YANK. The
simulation library that YANK runs on, OpenMM, can run on CPU, CUDA, and OpenCL platforms. The following command will
check which platforms are available:

.. code-block:: bash

   $ yank platforms

You should see an output that looks like the following:

.. code-block:: bash

   Available OpenMM platforms:
    0 Reference
    1 CPU
    2 CUDA
    3 OpenCL

If your output is missing on option you expect, such as CUDA on Nvidia GPUs, then please check that you have correct
drivers for your GPU installed. Non-standard CUDA installations require setting specific environment variables;
please see the :ref:`appropriate section <non-standard-cuda>` for setting these variables.

|

.. _non-standard-cuda:

Configuring Non-Standard CUDA Install Locations
-----------------------------------------------

Multiple versions of CUDA can be installed on a single machine, such as on shared clusters. If this is the case, it may
be necessary to set environment variables to make sure that the right version of CUDA is being used for YANK. You will
need to know the full ``<path_to_cuda_install>`` and the location of that installation's ``nvcc`` program is (by
default it is at ``<path_to_cuda_install>/bin/nvcc``). Then run the following lines to set the correct variables:

.. code-block:: bash

   export OPENMM_CUDA_COMPILER=<path_to_cuda_install>/bin/nvcc
   export LD_LIBRARY_PATH=<path_to_cuda_install>/lib64:$LD_LIBRARY_PATH

You may want to add the new ``$OPENMM_CUDA_COMPILER`` variable and ``$LD_LIBRARY_PATH`` extension to you ``~/.bashrc``
file to avoid setting this every time. If ``nvcc`` is installed in a different folder than the example, please use the
correct path for your system.


.. _shared-excluded-cuda:

Configuring Your CUDA Devices
-----------------------------

You will need to configure your CUDA devices to run in ``shared``/``Default`` Compute Mode
if you have CUDA based cards, especially if you plan to run MPI on multiple CUDA cards.

If you run ``nvidia-smi`` on your device, you will see a sample output that looks like this:

.. code-block:: bash

    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 367.48                 Driver Version: 367.48                    |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  GeForce GTX 680     Off  | 0000:03:00.0     N/A |                  N/A |
    | 30%   33C    P8    N/A /  N/A |      0MiB /  4036MiB |     N/A      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  GeForce GTX 680     Off  | 0000:04:00.0     N/A |                  N/A |
    | 30%   32C    P8    N/A /  N/A |      0MiB /  4036MiB |     N/A      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  GeForce GTX 680     Off  | 0000:83:00.0     N/A |                  N/A |
    | 30%   33C    P8    N/A /  N/A |      0MiB /  4036MiB |     N/A      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  GeForce GTX 680     Off  | 0000:84:00.0     N/A |                  N/A |
    | 30%   33C    P8    N/A /  N/A |      0MiB /  4036MiB |     N/A      Default |
    +-------------------------------+----------------------+----------------------+

The ``Compute M.`` on the right side should be set to ``Default`` for your device(s). If not, you can set the card(s)
mode with the following: ``nvidia-smi -i <List of Dev IDs> -c 0`` where ``<List of Dev IDs>`` is a comma separated list
of GPU indices no spaces. For this case, you can write:  ``nvidia-smi -i 0,1,2,3 -c 0``.

YANK also has the ability to check this status for you through ``yank selftest``. Part of the command will attempt to run
``nvidia-smi`` and infer what the Compute Mode is of any CUDA capable GPU detected. The command tries to infer this
information from parsing the output so it may not be exact. Please double check this yourself.

|

.. _optional_tools:

Optional Tools
--------------

The `OpenEye toolkit and Python wrappers <http://www.eyesopen.com/toolkits>`_ can be installed to enable free energy calculations to be set up directly from `multiple supported small molecule formats <https://docs.eyesopen.com/toolkits/python/oechemtk/molreadwrite.html#file-formats>`_, including

* SDF
* SMILES
* IUPAC names
* Tripos mol2
* PDB

Note that PDB and mol2 are supported through the pure AmberTools pipeline as well, though this does not provide access to the OpenEye AM1-BCC charging pipeline.

Use of the OpenEye toolkit requires an `academic or commercial license <http://www.eyesopen.com/licensing-philosophy>`_.

To install these tools into your conda environment, use `pip`:

.. code-block:: bash

   $ pip install -i https://pypi.anaconda.org/OpenEye/simple OpenEye-toolkits

Note that you will need to configure your ``$OE_LICENSE`` environment variable to point to a valid license file.

You can test your OpenEye installation with

.. code-block:: bash

   $ python -m openeye.examples.openeye_tests

|

Supported platforms and environments
====================================

Software
--------

YANK runs on Python 3.5, and Python 3.6

We no longer support Python 2.X.

Dependencies
++++++++++++

YANK uses a number of tools in order to allow the developers to focus on developing efficient algorithms involved in
alchemical free energy calculations, rather than reinventing basic software, numerical, and molecular simulation infrastructure.

.. warning:: Installation of these prerequisites by hand is not recommended---all required dependencies can be installed via the `conda <https://www.continuum.io/content/conda-data-science>`_  package manager.

.. note:: This list is taken directly from YANK's `conda-recipe/meta.yaml <https://github.com/choderalab/yank/blob/master/devtools/conda-recipe/meta.yaml>`_ to provide a singular source for dependencies


.. literalinclude:: ../devtools/conda-recipe/meta.yaml
    :language: yaml
    :start-after: requirements:
    :end-before: test:

Optional
^^^^^^^^

* `mpi4py <http://mpi4py.scipy.org/>`_ is needed if `MPI support <https://de.wikipedia.org/wiki/Message_Passing_Interface>`_ is desired.

.. note:: The ``mpi4py`` installation must be compiled against the system-installed MPI implementation used to launch jobs.
    Using the ``conda`` version of ``mpi4py`` together with the ``conda``-provided ``mpirun`` is the simplest way to avoid any issues.

.. In order to get stuff aligned up in this long bullet and be human readable, I used a sub. Hyperlink needed double _ to parse right

* |OEBullet|

.. |OEBullet| replace::
    The `OpenEye toolkit and Python wrappers <http://www.eyesopen.com/toolkits>`__ can be used to enable free energy
    calculations to be set up directly from multiple supported OpenEye formats, including Tripos mol2, PDB, SMILES, and
    IUPAC names (requires academic or commercial license).
    Note that PDB and mol2 are supported through the pure AmberTools pipeline as well, though this does not provide access
    to the OpenEye AM1-BCC charging pipeline.

* `cython <http://cython.org>`_ optional dependency for the replica-exchange code.

Hardware
--------

.. _supported_hardware:

Supported hardware
++++++++++++++++++

YANK makes use of `OpenMM <http://www.openmm.org>`_, a GPU-accelerated framework for molecular simulation.
This allows the calculations to take advantage of hardware that supports CUDA (such as NVIDIA GPUs) or OpenCL (NVIDIA and ATI GPUs, as well as some processors).
OpenMM also supports a multithreaded CPU platform which can be used if no CUDA or OpenCL resources are available.

OpenMM requires that AMD cards can support the most recent Catalyst drivers, and NVIDIA cards can support at least CUDA
7.5.

Recommended Hardware
++++++++++++++++++++

We have found the best price/performance results are currently obtained with NVIDIA GTX-class consumer-grade cards,
such as the GTX-780, GTX-980, GTX-1080, and GTX-Titan cards.
You can find some benchmarks for OpenMM on several classes of recent GPUs at `openmm.org <http://openmm.org/about.html#benchmarks>`_.

Ross Walker and the Amber GPU developers maintain a set of
`excellent pages with good inexpensive GPU hardware recommendations <http://ambermd.org/gpus/recommended_hardware.htm>`_
that will also work well with OpenMM and YANK.

.. _install_from_source:

Installing from Source
======================

.. note:: We recommend only developers wanting to modify the YANK code should install from source. Users who want to use
    the latest development version are advised to install the :ref:`Development build conda package <yank-dev-conda-package>` instead.

Installing from the GitHub Source Repository
--------------------------------------------

Installing from source is only recommended for developers that wish to modify YANK or the algorithms it uses.
Installation via ``conda`` is preferred for all other users.

Clone the source code repository from `GitHub <http://github.com/choderalab/yank>`_.

.. code-block:: bash

   $ git clone git@github.com:choderalab/yank.git
   $ cd yank/
   $ python setup.py install

If you wish to install into a different path (often preferred for development), use

.. code-block:: bash

   $ python setup.py install

``setup.py`` will try to install some of the dependencies, or at least check that you have them installed and throw an
error.
Note that not all dependencies can be installed via ``pip``, so you will have to install dependencies if installation
fails due to unmet dependencies.

Testing your Installation
-------------------------

Test your YANK installation to make sure everything is behaving properly on your machine:

.. code-block:: bash

   $ yank selftest

This will not only check that installation paths are correct, but also run a battery of tests that ensure any automatically
detected GPU hardware is behaving as expected. Please also check that YANK has access to the
:ref:`expected platforms <yank-platforms>` and the :ref:`correct CUDA version <non-standard-cuda>` if CUDA is installed
in a non-standard location.

Running on the Cloud
--------------------

Amazon EC2 now provides
`Linux GPU instances <http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using_cluster_computing.html>`_ with
high-performance GPUs and inexpensive on-demand and
`spot pricing <http://aws.amazon.com/ec2/purchasing-options/spot-instances/>`_ (g2.2xlarge).
We will soon provide ready-to-use images to let you quickly get started on EC2.

We are also exploring building `Docker containers <https://hub.docker.com/>`_ for rapid, reproducible, portable
deployment of YANK to new compute environments. Stay tuned!
