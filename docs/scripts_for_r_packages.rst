######################################
Writing wrapper scripts for R packages
######################################

Wrapping R package functions and making them available comprises three steps: writing and testing the wrapper scripts and adding to a Bioconda recipe.

**************************
Prerequisites
**************************

The worfkowscriptscommon package should be installed prior to starting package development. It will be added as a requirement in the Bioconda recipe so we know it will be available in production. For testing purposes you can install it from directly from the Git repo:

.. highlight:: R
devtools::install_github('https://github.com/ebi-gene-expression-group/workflowscriptscommon')
.. highlight:: none

**************************
Writing scripts themselves
**************************

Wrapper scripts should be written using the approach demonstrated in https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/seurat-read-10x.R. Specifically:

Use 'env' to locate the Rscript binary in the hash-bang:

.. highlight:: bash
#!/usr/bin/env Rscript 
.. highlight:: none

Then load the package 'optparse' (for option parsing) and the common functions package:

.. highlight:: R
suppressPackageStartupMessages(require(optparse))
suppressPackageStartupMessages(require(workflowscriptscommon))
.. highlight:: none

Note the use of suppressPackageStartupMessages to make sure we don't ouput more junk to stdout than we need to.

.. highlight:: R
option_list = list(
  make_option(
    c("-d", "--data-dir"),
    action = "store",
    default = NA,
    type = 'character',
    help = "Directory containing the matrix.mtx, genes.tsv, and barcodes.tsv files provided by 10X. A vector or named vector can be given in order to load several data directories. If a named vector is given, the cell barcode names will be prefixed with the name."
  ),
  make_option(
    c("-o", "--output-object-file"),
    action = "store",
    default = NA,
    type = 'character',
    help = "File name in which to store serialized R matrix object."
  )
)
.. highlight:: none


*******************
Writing test script
*******************

***********************
Writing Bioconda recipe
***********************


