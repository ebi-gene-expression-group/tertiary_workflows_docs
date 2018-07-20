############################
Currently created components
############################

The following component projects are ongoing:

* `workflowscriptscommon <https://github.com/ebi-gene-expression-group/workflowscriptscommon>`_ - Common functions for re-use over R package wrappers
* `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`- Wrapper scripts around Seurat R functions
* `bioconductor-singlecellexperiment-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>` - Wrapper scripts around SingleCellExperiment R functions
* `bioconductor-scater-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>` - Wrapper scripts around SingleCellExperiment R functions
* `container-galaxy-sc-tertiary <https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary>` - Galaxy container for single cell RNA-Seq tertiary analysis tools

########
Progress
########

**********
Clustering
**********

Seurat
======

Wrappers for individual Seurat steps have been written: https://github.com/ebi-gene-expression-group/r-seurat-scripts, and added to a Bioconda recipe (not yet submitted). 

Central functions of the Seurat workflow have been wrapped and are ready for use in workflows. However intermediate formats are currrently dominated by serialized R objects. Additional formats suitable for exchange with other tools willl be implemented in future.

SingleCellExperiment
====================

Wrappers for individual SingleCellExperiment steps have been written: https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts, and added to a Bioconda recipe (not yet submitted). 

Outputs a serialised R object of type 'SingleCellExperiment'.

Scater
======

Work started on Scater wrapper scripts: https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts.
