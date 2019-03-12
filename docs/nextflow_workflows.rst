###########################################
Nextflow workflows
###########################################

`Nextflow <https://www.nextflow.io/>`_ is a tool for writing reproducible workflows with minimal syntax and setup, and many of the components we have built are suitable for integration into workflows in this way.  

*****
Setup
*****

Nextflow is available on Bioconda, so can be installed simply, like:

.. code-block:: bash
    
    conda install Nextflow


*****************************
Currently available workflows
*****************************

The only Nextflow workflow we have made available so far is `scanpy-workflow <https://github.com/ebi-gene-expression-group/scanpy-workflow>`_ connecting elements of `scanpy-scripts <https://github.com/ebi-gene-expression-group/scanpy-scripts>`_. 

See the above link for documentation, but briefly this workflow can be executed with a command like:

.. code-block:: bash

    nextflow run -config <your nextflow.config> \
        ebi-gene-expression-group/scanpy-workflow \
        --matrix <mtx zip> --gtf <gtf> \
        --resultsRoot <final results dir>

Nextflow will download the workflow automatically before running it. You can of course clone the repository yourselve and make any desired customisations.

**************************
Writing Nextflow workflows
**************************

You can of course build workflows using any of the script components we have made available (see :ref:`_script_components`), using Nextflow in any manner you choose. However folowing some standards will make your work re-usable.

Using basic pipeline-sharing conventions
========================================

Following a few basic conventions will make your pipeline installable and re-usable using commands like the above- see the `Nextflow documentation <https://www.nextflow.io/docs/latest/sharing.html>`_ to see how this is done. Some key points:

* Put any executables you need to package with the workflow under bin/
* Name the workflow file 'main.nf' 
* Include as much default configuration as is necessary to make the workflow run

We strongly suggest using the Conda functionality provided by Nextflow, it makes installing software dependencies (including our components) much simpler. Using static environment files referred to in each process, rather than just listing the software each time, will make it easier to manage versions etc in the long run (see `scanpy-workflow <https://github.com/ebi-gene-expression-group/scanpy-workflow>`_). 

nf-core
=======

The `nf-core <https://nf-co.re/>`_ repository is emerging as a standard for building and distributing Nexflow workflows. It's requirements are quite stringent in the cause of producing good-quality workflows, but if you're starting from scratch you may find it beneficial to follow those conventions from the start.
