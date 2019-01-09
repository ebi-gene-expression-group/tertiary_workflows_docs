############################
Components
############################

We are building components in the following categories:

* Wrappers script packages for common single-cell analysis toolkits
* Bioconda packages to facilitate wrapper availability
* Bioconda packages for any previously missing packages
* Containers for Bioconda packages (mostly created automatically via Bioconda)
* Galaxy wrappers for Bioconda-packages wrappers

This page details components developed as part of this project..

*************************************
Wrapper script packages
*************************************

* `workflowscriptscommon <https://github.com/ebi-gene-expression-group/workflowscriptscommon>`_ - Common functions for re-use over R package wrappers
* `bioconductor-singlecellexperiment-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>`_ - Wrapper scripts around SingleCellExperiment R functions
* `dropletutils-scripts <https://github.com/ebi-gene-expression-group/dropletutils-scripts>`_ - Wrapper scripts for DropletUtils R functions
* `bioconductor-scater-scripts <https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts>`_ - Wrapper scripts around Scater R functions
* `bioconductor-sc3-scripts <https://github.com/ebi-gene-expression-group/bioconductor-sc3-scripts>`_ - Wrapper scripts around SC3 R functions
* `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_ - Wrapper scripts around Seurat R functions
* `scanpy-scripts <https://github.com/ebi-gene-expression-group/scanpy-scripts>`_ - Wrapper scripts for Scanpy functions

*****************
Bioconda packages
*****************

The above packages have been made available via Bioconda in the packages:

* `r-workflowscriptscommon <https://anaconda.org/bioconda/r-workflowscriptscommon>`_
* `bioconductor-singlecellexperiment-scripts <https://anaconda.org/bioconda/bioconductor-singlecellexperiment-scripts>`_
* `dropletutils-scripts <https://anaconda.org/bioconda/dropletutils-scripts>`_
* `bioconductor-scater-scripts <https://anaconda.org/bioconda/bioconductor-scater-scripts>`_
* `bioconductor-sc3-scripts <https://anaconda.org/bioconda/bioconductor-sc3-scripts>`_
* `r-seurat-scripts <https://anaconda.org/bioconda/r-seurat-scripts>`_
* `scanpy-scripts <https://anaconda.org/bioconda/scanpy-scripts>`_

We also added the previously missing Bioconda package for the DropletUtils Bioconductor package itself:

* `bioconductor-dropletutils <https://anaconda.org/bioconda/bioconductor-dropletutils>`_

**********
Containers
**********

Use of Bioconda packages means we get containers 'for free' as well. See containers for:

* `r-workflowscriptscommon <https://quay.io/repository/biocontainers/r-workflowscriptscommon>`_
* `bioconductor-singlecellexperiment-scripts <https://quay.io/repository/biocontainers/bioconductor-singlecellexperiment-scripts>`_
* `bioconductor-dropletutils <https://quay.io/repository/biocontainers/bioconductor-dropletutils>`_
* `dropletutils-scripts <https://quay.io/repository/biocontainers/dropletutils-scripts>`_
* `bioconductor-scater-scripts <https://quay.io/repository/biocontainers/bioconductor-scater-scripts>`_
* `bioconductor-sc3-scripts <https://quay.io/repository/biocontainers/bioconductor-sc3-scripts>`_
* `r-seurat-scripts <https://quay.io/repository/biocontainers/r-seurat-scripts>`_
* `scanpy-scripts <https://quay.io/repository/biocontainers/scanpy-scripts>`_

We have also developed the following:

* `container-galaxy-sc-tertiary <https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary>`_ - Galaxy container for single cell RNA-Seq tertiary analysis tools

*****************
Galaxy components
*****************



