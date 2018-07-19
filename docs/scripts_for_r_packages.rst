######################################
Writing wrapper scripts for R packages
######################################

.. highlight:: html

The literal blocks are now highlighted as HTML, until a new directive is found.

::
   <html><head></head>
   <body>This is a text.</body>
   </html>

The following directive changes the hightlight language to SQL.


Wrapping R package functions and making them available comprises three steps: writing and testing the wrapper scripts and adding to a Bioconda recipe.

**************************
Prerequisites
**************************

The worfkowscriptscommon package should be installed prior to starting package development. It will be added as a requirement in the Bioconda recipe so we know it will be available in production. For testing purposes you can install it from directly from the Git repo:


**************************
Writing scripts themselves
**************************

Wrapper scripts should be written using the approach demonstrated in https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/seurat-read-10x.R. Specifically:

Use 'env' to locate the Rscript binary in the hash-bang:


Then load the package 'optparse' (for option parsing) and the common functions package:


Note the use of suppressPackageStartupMessages to make sure we don't ouput more junk to stdout than we need to.



*******************
Writing test script
*******************

***********************
Writing Bioconda recipe
***********************


