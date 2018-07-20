######################################
Writing wrapper scripts for R packages
######################################

**************************
Prerequisites
**************************

workflowscriptscommon R package
===============================

This package will be provided in production via a Conda recipe. For development purposes it can be installed directly from GitHub:

.. code-block:: r

    devtools::install_github('https://github.com/ebi-gene-expression-group/workflowscriptscommon')

Functions
-------------------

The workflowscriptscommon package defines some simple functions that will be of use in writing wrappers:

* wsc_parse_args() wraps the use of optparse's OptionParser() for convenience, allowing checks for mandatory arguments
* wsc_parse_numeric() parses numeric vectors out of strings
* wsc_split_string() parses character vectors out of strings

**************************
Define inputs and outputs 
**************************

You will be writing scripts to wrap one or more functions from an R package. Look at those functions' documentation (?function) record their inputs and outputs their types, and decide how those arguments will be processed by the script. 

Suggested patterns:

* string arguments can be passed directly from the command line
* short string or numeric vectors can be passed as comma-separated strings
* longer vectors (gene lists etc) can be supplied as files with one entry per line, read via readLines() and written via writeLines()

***************
Writing scripts
***************

The wrapper scripts should be written using the approach demonstrated in https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/seurat-read-10x.R. Specifically:

Use 'env' to locate the Rscript binary in the hash-bang:

.. code-block:: bash

    #!/usr/bin/env Rscript 

Then load the package 'optparse' (for option parsing) and the common functions package:

.. code-block:: r

    suppressPackageStartupMessages(require(optparse))
    suppressPackageStartupMessages(require(workflowscriptscommon))

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

Processing and outputs
======================

The above done, feel free to load the package whose functions you're wrapping, and write the processing functionality:

.. code-block:: r

    suppressPackageStartupMessages(require(Seurat))

Once you have added processing code, pay attention to the output formats you use. R objects should be serialised using saveRDS(), and where feasible additional text-based formats should be used. Even complex R objects will eventually need to be output as formats readable by e.g. Python, but this not essential right now. 

As a final point, make sure all wrapper scripts are executable:

.. code-block:: console

    chmod +x <script>

