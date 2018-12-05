.. Read the Docs Template documentation master file, created by
   sphinx-quickstart on Tue Aug 26 14:19:49 2014.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

###############################################
Tertiary workflows for single-cell RNA-seq data
###############################################

*******
Project
*******

Tertiary workflows in single-cell RNA-seq analysis are, in the definition of the Human Cell Atlas (HCA), those processes occuring subsequent to quantification, such as clustering and trajectory inference. Example libraries implementing these analyses are Seurat, Scater and Scanpy.

This is a project to increase the re-usability and reproducibility of these workflows, to facilitate comparison and benchmarking. Our strategy is:

1. Provide command-line access to individual library functions through simple wrapper scripts packaged with Bioconda.
2. Enable pipeline inter-operability by adopting and/or improving file format standards (e.g. Loom) which can be read and written by a variety of packages.
3. Facilitate the generation of workflows built on the above components, in environments such as Galaxy and Nextflow, for deployment in platform-agnostic ways.
   

.. toctree::
   :maxdepth: 2
   :caption: General

   overview
   authors

.. toctree::
   :maxdepth: 3
   :caption: Components

   modules

.. toctree::
   :caption: Using workflow components

   running_galaxy_sc_locally   

.. toctree::
   :maxdepth: 2
   :caption: Contribution guidelines

   writing_wrapper_scripts


..   adding_scripts_to_wrapper_packages
     galaxy_community
     bioconda_wrapping_tools
    writing_planemo_galaxy_wrapper
    writing_documentation

