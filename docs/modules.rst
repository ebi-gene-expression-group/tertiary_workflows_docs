############################
Currently created components
############################

See https://github.com/orgs/ebi-gene-expression-group/teams/czi-portals/repositories for a complete list of of all repsoitories associated with this project. The below cover these in more detail.

******************
Shared R functions
******************

Wrapping R packages requires some common functions. These are provided in an R package: https://github.com/ebi-gene-expression-group/workflowscriptscommon.

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
