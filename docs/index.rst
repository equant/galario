.. galario documentation master file, created by
   sphinx-quickstart on Wed May 24 13:32:58 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

=========
|galario|
=========

**GPU Accelerated Library for Analysing Radio Interferometer Observations**
---------------------------------------------------------------------------

|galario| is a library that exploits the computing power of modern graphic cards (GPUs) to accelerate the comparison of model
predictions to radio interferometer observations. Namely, it speeds up the computation of the synthetic visibilities
given a model image (or an axisymmetric brightness profile) and their comparison to the observations.

Along with the GPU accelerated version based on the
`CUDA Toolkit <https://developer.nvidia.com/cuda-toolkit>`_ offers a CPU counterpart accelerated with
`openMP <http://www.openmp.org>`_.
Modern radio interferometers like
`ALMA <http://www.almaobservatory.org/en/home/>`_,
`VLA <https://science.nrao.edu/facilities/vla>`_,
`NOEMA <http://www.iram-institute.org/EN/noema-project.php?ContentID=9&rub=9&srub=0&ssrub=0&sssrub=0>`_
are pushing the computational efforts needed to model the observations to the extreme.
The unprecedented sensitivity and resolution achieved by these observatories require comparing model predictions
with huge amount of data points that sample a wide range of spatial frequencies.
In this context, |galario| provides a fast library useful for comparing a model to observations directly in the Fourier plane.

We presented |galario| in `Tazzari, Beaujean and Testi (2017) <LINK>`_, where you can find more details about the
relevant equations and the algorithm implementation.
Here we do not aim to summarize the vast literature about Radio Interferometry, but we refer the interested reader to the
`Synthesis Imaging in Radio Astronomy II <http://aspbooks.org/a/volumes/table_of_contents/180>`_ book.

|galario| is actively developed on `GitHub <https://github.com/mtazzari/galario/>`_
and has already been employed in :doc:`these published studies <studies>`.

Instructions on how to build and install |galario| can be found :doc:`here <install>`.


Basic Usage
-----------
.. |u_j| replace:: :math:`u_j`
.. |v_j| replace:: :math:`v_j`
.. |w_j| replace:: :math:`w_j`

Let's say you have an observational dataset of `M` visibility points located at :math:`(u_j, v_j)`, with :math:`j=1...M` and |u_j|, |v_j|
expressed in units of the observing wavelength. :math:`V_{obs\ j}` (Jy) is the :math:`j`-th complex visibility with associated theoretical weight |w_j|.
If you want to compute the visibilities of a model `image` (Jy/px) with pixel size `dxy` (rad) in the same :math:`(u_j, v_j)` locations of the observations,
you can easily do it with the GPU accelerated |galario|:

.. code-block:: python

    from galario.double_cuda import sampleImage

    vis = sampleImage(image, dxy, u, v, dRA=dRA, dDec=dDec, PA=PA)

where `vis` is a complex array of length :math:`N` containing the real (`vis.real`) and imaginary (`vis.imag`) part of the synthetic visibilities.
dRA, dDec and PA are optional parameters: if specified, translate the image in Right Ascension and Declination direction
by dRA (rad) and dDec (rad), respectively, and to rotate it by a Position Angle PA (rad) (East of North).

If you are doing a **fit** and the only number you are interested in is the **chi square** needed for the likelihood computation,
you can use directly:

.. code-block:: python

    from galario.double_cuda import chi2Image

    chi2 = chi2Image(image, dxy, u, v, V_obs.real, V_obs.imag, w)

If you want to compare the observations with a model characterized by an **axisymmetric brightness profile**, |galario| offers
dedicated functions that exploit the symmetry of the model to accelerate the image creation.
If :math:`I(R)` (Jy/sr) is the radial brightness profile, the command is as simple as:

.. code-block:: python

    from galario.double_cuda import sampleProfile

    vis = sampleProfile(I, Rmin, dR, nxy, dxy, u, v)
.. add an example with inc, PA, dRA, dDec?

where `Rmin` and `dR` are expressed in radians and are the innermost radius and the cell size of the grid on which :math:`I(R)` is computed. An analogous function
`chi2Profile` allows one to compute directly the chi square.

.. note::
    If you work on a machine **without** a CUDA-enabled GPU, don't worry: you can use the CPU version
    of |galario| by just removing the subscript `"_cuda"` from the imports above and benefit from the openMP parallelization.
    All the function names and interfaces are the same for GPU and CPU version!

.. More details on how to get started with |galario| are given in the :doc:`quickstart <quickstart>`.

Be sure to checkout also the :doc:`cookbook <cookbook>` with many useful code snippets!

License and Attribution
-----------------------
If you use |galario| for your research, please cite `Tazzari, Beaujean and Testi (2017) <LINK>`_ .
The BibTeX entry for the paper is::

    @ARTICLE{...}

|galario| is free software licensed under the LGPLv3 License. For more details see the :doc:`LICENSE <license>`.

© Copyright 2017 Marco Tazzari, Frederik Beaujean, Leonardo Testi.

Contents
--------
.. toctree::
    :maxdepth: 2

    install
    cookbook
    py-api
    C-api
    studies
    license
..     quickstart

Indices
-------
* :ref:`genindex`
* :ref:`search`

