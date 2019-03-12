############################
Components
############################

.. contents::

We are building components in the following categories:

* Wrappers script packages for common single-cell analysis toolkits
* Bioconda packages to facilitate wrapper availability
* Bioconda packages for any previously missing packages
* Containers for Bioconda packages (mostly created automatically via Bioconda)
* Galaxy wrappers for Bioconda-packages wrappers, and associated workflows
* Nextflow workflows built on packaged script components

This page details these components.

.. _script_components:

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
* `sc3-scripts <https://anaconda.org/bioconda/sc3-scripts>`_
* `seurat-scripts <https://anaconda.org/bioconda/seurat-scripts>`_
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
* `sc3-scripts <https://quay.io/repository/biocontainers/sc3-scripts>`_
* `seurat-scripts <https://quay.io/repository/biocontainers/seurat-scripts>`_
* `scanpy-scripts <https://quay.io/repository/biocontainers/scanpy-scripts>`_

We have also developed the following:

* `container-galaxy-sc-tertiary <https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary>`_ - Galaxy container for single cell RNA-Seq tertiary analysis tools

*****************
Galaxy components
*****************

The following components will shortly be available in a Galaxy 'toolshed'. These are all powered by the same scripts and Conda packages described above, and in this way analyses can be made consistent, whatever workflow construct is used to connect them.

Scanpy
======

(currently at v1.3.2)

* Read10x - Read 10x into hdf5 object handled by scanpy
* Filter cells - FilterCells based on counts and numbers of genes expressed
* Filter genes - Filter genes based on counts and numbers of cells expressed
* NormaliseData - Normalise data to make all cells having the same total expression
* FindVariableGenes - Find variable genes based on normalised dispersion of expression
* ScaleData - Scale data to make expression variance the same for all genes
* Run PCA - Run PCA for dimensionality reduction
* ComputeGraph - Compute graph to derive kNN graph
* FindCluster - Find clusters based on community detection on KNN graph
* RunUMAP - Run UMAP to visualise cell clusters using UMAP
* RunTSNE - Run tSNE to visualise cell clusters using tSNE
* FindMarkers - FindMarkers to find differentially expressed genes between groups

R/Bioconductor SingleCellExperiment ecosystem
=============================================

DropletUtils
------------

(currently at v1.0.3)

Read 10x data into a SingleCellExperiment object

Scater
------

(currently at v1.8.4)

* CalculateCPM	- CalculateCPM from raw counts
* Normalise	- Normalise expression values by library size in log2 scale
* CalculateQcMetrics - CalculateQcMetrics based on expression values and experiment information
* DetectOutlier - DetectOutlier cells based on expression metrics
* Filter - Filter cells and genes based on pre-calculated stats and QC metrics

SC3
---

(currently at v1.8.0)

* Prepare - Prepare a sc3 SingleCellExperiment object
* Estimate -Estimate the number of clusters for k-means clustering
* Calculate - Transformations Calculate Transformations of distances using PCA and graph Laplacian
* Calculate - Distances Calculate Distances between cells
* K-Means - K-Means perform k-means clustering
* Calculate - Consensus Calculate Consensus from multiple runs of k-means clustering
* DiffExp - Calculates DE genes, marker genes and cell outliers

Seurat
======

(currently at v2.3.1)

* Read10x - Loads 10x data into a serialized seurat R object
* CreateSeuratObject - create a Seurat object
* FilterCells - filter cells in a Seurat object
* NormaliseData - normalise data
* FindVariableGenes - identify variable genes
* ScaleData - scale and center genes
* RunPCA - run a PCA dimensionality reduction
* RunTSNE - run t-SNE dimensionality reduction
* Plot dimension reduction - graphs the output of a dimensional reduction technique (PCA by default). Cells are colored by their identity class.
* FindClusters - find clusters of cells
* FindMarkers - find markers (differentially expressed genes)
* Export2CellBrowser - Export2CellBrowser produces files for UCSC CellBrowser import.

UCSC Cell Browser
=================

(currently at 0.4.38)

UCSC Cell Browser displays single-cell clusterized data in an interactive web application.

*******************
Low-level workflows
*******************

The following `Nextflow <https://www.nextflow.io/>`_ workflows are available:

* `scanpy-workflow <https://github.com/ebi-gene-expression-group/scanpy-workflow>`_ - This is a fully pameterised workflow linking the components of  `scanpy-scripts <https://github.com/ebi-gene-expression-group/scanpy-scripts>`_.

