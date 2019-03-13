######################################
Writing wrapper scripts for R packages
######################################

**************************
Recommended resources
**************************

optparse
========

We recommend use of the `optparse <https://cran.r-project.org/web/packages/optparse/index.html>`_ package for development of user-friendly R scripts. 

workflowscriptscommon R package
===============================

We have built the `workflowscriptscommon <https://github.com/ebi-gene-expression-group/workflowscriptscommon>`_ package to hold functions common to R script wrapper writing. Using this package (and adding functions where required) is a useful way of reducing repetition between R wrappers.

The package is available in Bioconda and can be installed using conda, like:

.. code-block:: bash

    conda install r-workflowscriptscommon

Existing functions
-------------------

The workflowscriptscommon package defines some simple functions that will be of use in writing wrappers:

* wsc_parse_args() wraps the use of optparse's OptionParser() for convenience, allowing checks for mandatory arguments
* wsc_parse_numeric() parses numeric vectors out of strings
* wsc_split_string() parses character vectors out of strings
* wsc_read_vector() parses a named vector from a two-column file
* wsc_write_vector() writes a named vector to a two-column file

New functions
-------------

If you develop further functions that might be of use across other wrapper scripts, you can propose them for addtion to this package via pull request to the develop branch of the source repository at https://github.com/ebi-gene-expression-group/workflowscriptscommon. If you need to use new functions before they are added to a release and made available via the Bioconda package, you can install the R package directly from GitHub:

.. code-block:: r

    devtools::install_github('https://github.com/ebi-gene-expression-group/workflowscriptscommon', ref='develop')

**************************
Define inputs and outputs 
**************************

The first step in writing a wrapper script is to define inputs and outputs and which function(s) are required to run the desired process. Look at those functions' documentation (?function), record their inputs and outputs their types, and decide how those arguments will be processed by the script. 

Suggested patterns:

* string arguments can be passed directly from the command line
* short string or numeric vectors can be passed as comma-separated strings
* longer vectors (gene lists etc) can be supplied as files with one entry per line, read via readLines() and written via writeLines()

***************
Writing scripts
***************

Each wrapper script should be named to reflect the r package and the functions called as closely as possible. For example a wrapper for the single calculateCPM() function of scater will be called scater-calculate-cpm.R. For more complex wrappers involving multiple function calls, make sure your naming is sensible and easily interpretable by the user. We prefer hyphenated script names.

An example wrapper scripts is `seurat-read-10x.R <https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/seurat-read-10x.R>`_ from the r-seurat-scripts package. Some useful conventions demonstrated there are:

Use 'env' to locate the Rscript binary in the hash-bang:

.. code-block:: bash

    #!/usr/bin/env Rscript 

Then load the package 'optparse' (for option parsing) and the common functions package:

.. code-block:: r

    suppressPackageStartupMessages(library(optparse))
    suppressPackageStartupMessages(library(workflowscriptscommon))

Note the use of suppressPackageStartupMessages to make sure we don't ouput more junk to stdout than we need to. DO NOT put the command for loading the main R package here (for example Seurat). Loading such packages can take a surprisingly long time, and we need to make sure our command-line arguments are good before committing to it.

Argument parsing
================

Now write the code that will parse command line options in the finished script. Use make_option() from the optparse package to parse the command line arguments into a list of objects as defined by your examination of inputs and outputs defined above. Here is an example of the 10x data parsing function, taken from a wrapper in the r-seurat-scripts package:

.. code-block:: r

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

This takes two character arguments specifying input and output files. We can then use one of the functions mentioned above to parse out the actual argument values whilst checking that no mandatory arguments are missing:

.. code-block:: r

    opt <- wsc_parse_args(option_list, mandatory = c('input_object_file', 'output_object_file'))

You may also want to check the values yourself, for example to see if files specified are actually present:

.. code-block:: r

    # Check parameter values

    if ( ! file.exists(opt$input_object_file)){
      stop((paste('Directory', opt$input_object_file, 'does not exist')))
    }

Translating files to data structures
====================================    

When writing wrapper scripts pay careful attention to how the data types required for the wrapped R function relate to how that information is supplied to the wrapper script itself. For example, where input is a vector but it's likely to be very short (e.g. a list of gene biotypes), it might be acceptable to supply this list to the script in a simple comma-separated string, which can be parsed into a vector using wsc_split_string(). Longer lists (e.g. gene names) should be supplied in a single-column text file that can be parsed using readLines(). Where vector names are important, for example specifying set of values for a metadata variable for a list of cells, these should be supplied in two-column (label/value) rows which can then be parsed by wsc_read_vector(). 

These datatype handling operations will likely need to evolve- please contribute using the PR mechanism on the workflowscripts common package as mentioned above. 

Processing and outputs
======================

The above done, feel free to load the package whose functions you're wrapping, and write the processing functionality:

.. code-block:: r

    suppressPackageStartupMessages(library(Seurat))

Once you have added processing code, pay attention to the output formats you use. R objects should be serialised using saveRDS(), and where feasible additional text-based formats should be used. Even complex R objects will eventually need to be output as formats readable by e.g. Python, but this not essential right now. 

As a final point, make sure all wrapper scripts are executable:

.. code-block:: console

    chmod +x <script>

