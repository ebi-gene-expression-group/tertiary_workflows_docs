######################################
Writing wrapper scripts for R packages
######################################

Wrapping R package functions and making them available comprises three steps: writing and testing the wrapper scripts and adding to a Bioconda recipe, which will facilitate installation of those scripts on a production system.

**************************
Prerequisites
**************************

Bioconda
========

Install Bioconda (and Conda) using the instructions at https://bioconda.github.io/. 

You will also need conda-build:

.. code-block:: console

    conda install conda-build

workflowscriptscommon R package
===============================

This package will be provided in production via a Conda recipe. For development purposed it can be installed directly from GitHub:

.. code-block:: r

    devtools::install_github('https://github.com/ebi-gene-expression-group/workflowscriptscommon')

Functions
-------------------

The workflowscriptscommon package defines some simple functions that will be of use in writing wrappers:

* wsc_parse_args() wraps the use of optparse's OptionParser() for convenience, allowing checks for mandatory arguments
* wsc_parse_numeric() parses numeric vectors out of strings
* wsc_split_string() parses character vectors out of strings

***************************
Create emtpy Git repository
***************************

Create a repository named for the Bioconda package containing the functions you want to wrap, with a '-scripts' suffix. For example, the Seurat package is called 'r-seurat', so we create a package called r-seurat-scripts.

Create a README for this package similar to https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/README.md, and store all R scripts in the root of the repo.

**************************
Define inputs and outputs 
**************************

You will be writing a scripts to wrap one or more functions from an R package. Look at those functions' documentation (?function) record their inputs and outputs their types, and decide how those arguments will be processed by the script. 

Suggested patterns:

* string arguments can be passed directly from the command line
* short string or numeric vectors can be passed as comma-separated strings
* longer vectors (gene lists etc) can be supplied as files with one entry per line, read via readLines() and written via writeLines()

**************************
Writing scripts themselves
**************************

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

*******************
Writing test script
*******************

Once you have written all the wrapper scripts for the package, write a test Shell script that will be provided to the Conda package for testing purposes after installation. You should: 

* Retrieve test data from an online location (don't try and package test data with the scripts)
* Execute every script you have wrapped using the steps above

For the r-seurat-scripts exampel see https://github.com/ebi-gene-expression-group/r-seurat-scripts/blob/master/r-seurat-scripts-post-install-tests.sh.

Further guidelines for writing test scripts will be placed here soon.

***********************
Writing Bioconda recipe
***********************

A Bioconda recipe is a simple set of configuration files defining where software can be found, and how it may be installed. It is comprised primarily of a meta.yaml file to define metadata associated with a package, and a script, build.sh, which installs the software. See https://bioconda.github.io/contributing.html for detailed info.

To create a new recipe you will need to fork the bioconda-recipes repository from https://github.com/bioconda/bioconda-recipe if your you or your group does not already have a fork. Clone your fork, and from within that clone create a new branch named for your new package (e.g. r-seurat-scripts):

.. code-block:: console

    git checkout -b <package name>

You will eventually submit a request for this branch to be merged, once your development is complete.

Then create a directory for a new recipe:

.. code-block:: console

    mkdir -p recipes/<package name>

Within that new directory create your meta.yaml and build.sh. For r-seurat-scripts meta.yaml looks like:

.. code-block:: css

    {% set version = '0.0.1' %}

    package:
      name: r-seurat-scripts
      version: {{ version }}

    source:
      git_url: https://github.com/ebi-gene-expression-group/r-seurat-scripts.git

    requirements:
        build:
            - git
        run:
            - r-seurat
            - r-optparse
            - r-workflowscriptscommon

    test:
        commands:
            - which seurat-read-10x.R
            - which seurat-create-seurat-object.R
            - which seurat-normalise-data.R
            - which seurat-filter-cells.R
            - which seurat-find-variable-genes.R
            - which seurat-get-random-genes.R
            - which seurat-run-pca.R
            - which seurat-scale-data.R
            - which seurat-dim-plot.R
            - which seurat-find-clusters.R
            - which seurat-run-tsne.R
            - which seurat-find-markers.R
            - which r-seurat-scripts-post-install-tests.sh

* The download package is specified under 'source'. We should link to a static version once code has been stabilied, this just points the HEAD of a repository.
* Requirements define the dependencies you need. You'll need to specify the package you're wrapping here, as well as r-workflowscriptscommon.
* The test sections runs commands to check the installation has worked. DO NOT run any further testing here- Bioconda is only interested in whether the install works, downstream testing will be done by the user post-install.

r-seurat-script's build.sh script is very simple:

.. code-block:: bash

    #!/usr/bin/env bash

    mkdir -p $PREFIX/bin
    cp *.R $PREFIX/bin
    cp *.sh $PREFIX/bin

This simply copies the scripts to conda's build directory.

With these two files in place you can do a test local install of your Bioconda recipe. Make sure you're in the directory for your recipe and then:

.. code-block:: console
    
    conda build .
    conda install --force --use-local r-seurat-scripts

If you've done things correctly this will clone your package repository and install the scripts.
