#####################################################
Writing Bioconda recipes
#####################################################

A Bioconda recipe is a simple set of configuration files defining where software can be found, and how it may be installed. The effect is to allow installation via Conda in a 'package manager'-like interface. This section describes how to create Bioconda recipes providing wrapper scripts for individual components of toolkits such as Seurat. You will write wrapper scripts for all necessary components, create a test script to make sure your wrappers operate correctly, and finally write what's called a 'recipe' for Bioconda. When run via 'conda install' This recipe will download the scripts you've written (in this case from GitHub) and install them on host machines. 

**************************
Prerequisites
**************************

Working Bioconda installation
=============================

Before commencing development you must install Bioconda (and Conda) using the instructions at https://bioconda.github.io/. 

You will also need conda-build:

.. code-block:: console

    conda install conda-build

Fork of the bioconda-recipes repository
=======================================

To create a new recipe you will need to fork the bioconda-recipes repository from https://github.com/bioconda/bioconda-recipe if you or your group does not already have a fork. 

Emtpy Git repository
====================

The wrapper scripts you write will live in a GitHub repository from where they will be downloaded when the Bioconda recipe is run. Create a repository named for the Bioconda package containing the functions you want to wrap, with a '-scripts' suffix. For example, the Seurat package is called 'r-seurat', so we create a repository called r-seurat-scripts.

Create a README for this package similar to https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/README.md.

*********
Sub-tasks
*********

Creating Bioconda packages containg wrapper scripts for the individual steps of a given toolkit involves:

.. toctree::
    :maxdepth: 1

    writing_wrapper_scripts 
    writing_tests
    writing_bioconda_recipes

