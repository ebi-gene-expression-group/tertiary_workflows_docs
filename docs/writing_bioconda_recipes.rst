.. _writing_bioconda_recipes:

###########################
2. Create Bioconda packages
###########################

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
      url: https://github.com/ebi-gene-expression-group/r-seurat-scripts/archive/v{{ version }}.tar.gz
      sha256: 580770b74194b41656e39af542af01250326f41a311038249b84dede8a57f76c

    build:
      number: 0
      noarch: generic

    requirements:
    	run:
    		- r-base
    		- r-seurat>=2.3.1
    		- r-optparse
    		- r-workflowscriptscommon

    test:
    	commands:
    		- seurat-read-10x.R --help
    		- seurat-create-seurat-object.R --help
    		- seurat-normalise-data.R --help
    		- seurat-filter-cells.R --help
    		- seurat-find-variable-genes.R --help
    		- seurat-run-pca.R --help
    		- seurat-scale-data.R --help
    		- seurat-dim-plot.R --help 
    		- seurat-find-clusters.R --help
    		- seurat-run-tsne.R --help
    		- seurat-find-markers.R --help
    		- which seurat-get-random-genes.R
    		- which r-seurat-scripts-post-install-tests.sh

    about:
      home: https://github.com/ebi-gene-expression-group/r-seurat-scripts
      dev_url: https://github.com/ebi-gene-expression-group/r-seurat-scripts
      license: GPL-3
      summary: A set of wrappers for individual components of the Seurat package.
    	  Functions R packages are hard to call when building workflows outside of R,
    	  so this package adds a set of simple wrappers with robust argument parsing.
    	  Intermediate steps are currently mainly serialized R objects, but the
    	  ultimate objective is to have language-agnostic intermediate formats allowing
    	  composite workflows using a variety of software packages.
      license_family: GPL

Note that:

* The download package is specified under 'source'. It links to a static version with a checksum (see `the bioconda recipe guidelines <https://bioconda.github.io/guidelines.html#hashes>`_). There will need to be a tagged release on the source repository to make this possible.
* Requirements define the dependencies you need. You'll need to specify the package you're wrapping here, as well as r-workflowscriptscommon and r-optparse.
* The build section as used here (with 'generic') identifies the code as non-platform-specific.
* The test sections runs commands to check the installation has worked. Using --help makes sure that e.g. optparse has loaded correctly, as well as the scripts being installed. DO NOT run any further testing here- Bioconda is only interested in whether the install works, downstream testing will be done by the user post-install.

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

Submitting to Bioconda
**********************

Before submitting to Bioconda you will need to test the recipe in as close a manner as possible to how Bioconda does, in order to prevent wasting their continuous integration resources with buggy recipes. To do so, follow `the bioconda contribution guidelines <https://bioconda.github.io/contribute-a-recipe.html#test-locally>`_. Ideally, the CircleCI or mulled-build methods should be used, and will use containers to run the tests. This isn't always easy to get working, however, so at a minimum use the non-docker Conda method cited in the documentation:

.. code-block:: bash

    ./bootstrap.py --no-docker /tmp/miniconda
    source ~/.config/bioconda/activate
    bioconda-utils build recipes config.yml --git-range master

Assuming the tests complete successfully, you can `follow the instructions <https://bioconda.github.io/contribute-a-recipe.html#push-changes-wait-for-tests-to-pass-submit-pull-request>`_ to submit a pull request, request review etc. With that process complete, your recipe will become a pakage availabe for installation via Conda.
