.. _writing_bioconda_recipes:

########################
Writing Bioconda recipes
########################

A Bioconda recipe is comprised primarily of a meta.yaml file to define metadata associated with a package, and a script, build.sh, which installs the software. See https://bioconda.github.io/contributing.html for detailed info.

Clone your fork of bioconda-recipes, and from within that clone create a new branch named for your new package (e.g. r-seurat-scripts):

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
