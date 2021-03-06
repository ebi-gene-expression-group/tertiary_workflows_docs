#############
Writing tests
#############

Along with any collection of scripts you write for a package, you should provide  testing code which can be run by users after installation of your package (probably via Conda). The test suite per package is composed of at least:

* A bash script which sets up variables, defines inputs and outputs, and exports them, named for the package so that it does not clash with similar scripts when installed by Conda. So the package bioconductor-singlecellexperiment-scripts has a test script called bioconductor-singlecellexperiment-scripts-post-install-tests.sh.
* A set of 'bats' tests (see https://github.com/bats-core/bats-core) to make sure all the tools run. 

*********
Test data
*********

Do not package test data with your package. Instead include locations from which data can be downloaded during testing.

Example: bioconductor-singlecellexperiment-scripts
==================================================

The testing setup for `bioconductor-singlecellexperiment-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts/tree/devel>`_ at time of writing was as follows (it's probably evolved a bit now, but the example is still valid).

Bash script
-----------

There is a starting section with script usage, where we work out what the script is called and where it is:

.. code-block:: bash

    #!/usr/bin/env bash

    script_dir=$(cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )
    script_name=$0

    # This is a test script designed to test that everything works in the various
    # accessory scripts in this package. Parameters used have absolutely NO
    # relation to best practice and this should not be taken as a sensible
    # parameterisation for a workflow.

    function usage {
        echo "usage: $script_name [action] [use_existing_outputs]"
        echo "  - action: what action to take, 'test' or 'clean'"
        echo "  - use_existing_outputs, 'true' or 'false'"
        exit 1
    }

    action=${1:-'test'}
    use_existing_outputs=${2:-'false'}

    if [ "$action" != 'test' ] && [ "$action" != 'clean' ]; then
        echo "Invalid action"
        usage
    fi

    if [ "$use_existing_outputs" != 'true' ] && [ "$use_existing_outputs" != 'false' ]; then
        echo "Invalid value ($use_existing_outputs) for 'use_existing_outputs'"
        usage
    fi

The next section defines the remote locations of test data, and local paths to store them in. It also  allows for cleanup of test data (provided with the appropriate option):

.. code-block:: bash

    test_matrix_url="https://raw.githubusercontent.com/jdblischak/singleCellSeq/master/data/molecules.txt"
    test_annotation_url="https://raw.githubusercontent.com/jdblischak/singleCellSeq/master/data/annotation.txt"

    test_working_dir=`pwd`/'post_install_tests'
    export test_matrix_file=$test_working_dir/test_data/`basename $test_matrix_url`
    export test_annotation_file=$test_working_dir/test_data/`basename $test_annotation_url`

    # Clean up if specified

    if [ "$action" = 'clean' ]; then
        echo "Cleaning up $test_working_dir ..."
        rm -rf $test_working_dir
        exit 0
    elif [ "$action" != 'test' ]; then
        echo "Invalid action '$action' supplied"
        exit 1
    fi

    # Initialise directories

    output_dir=$test_working_dir/outputs
    data_dir=$test_working_dir/test_data

    mkdir -p $test_working_dir
    mkdir -p $output_dir
    mkdir -p $data_dir

    ################################################################################
    # Fetch test data
    ################################################################################

    if [ ! -e "$test_matrix_file" ]; then
        wget $test_matrix_url -P $data_dir
        wget $test_annotation_url -P $data_dir
    fi

Now there is a section where we define inputs and outputs, and where we would also store any parameter values we need the scripts to use during testing:

.. code-block:: bash

    ################################################################################
    # List tool outputs/ inputs
    ################################################################################

    export raw_singlecellexperiment_object="$output_dir/raw_sce.rds"

    ## Test parameters- would form config file in real workflow. DO NOT use these
    ## as default values without being sure what they mean.

Note the use of 'export'- only exported variables will be available for testing by bats.

Lastly we call the bats testing script, predicting its name from the name of the current script:

.. code-block:: bash

    ################################################################################
    # Test individual scripts
    ################################################################################

    # Make the script options available to the tests so we can skip tests e.g.
    # where one of a chain has completed successfullly.

    export use_existing_outputs

    # Derive the tests file name from the script name

    tests_file="${script_name%.*}".bats

    # Execute the bats tests

    $tests_file

Note the export of the 'use_existing_outputs' variable. Bats will be able to use this to decide whether or not to bother re-running a test.

Bats script
-----------

There's only one script in this package, so we have a single Bats test:

.. code-block:: bash

    #!/usr/bin/env bats

    @test "SingleCellExperiment creation" {
        if [ "$use_existing_outputs" = 'true' ] && [ -f "$raw_singlecellexperiment_object" ]; then
            skip "$use_existing_outputs $raw_singlecellexperiment_object exists and use_existing_outputs is set to 'true'"
        fi
        
        run rm -f $raw_singlecellexperiment_object && singlecellexperiment-create-single-cell-experiment.R -a $test_matrix_file -c $test_annotation_file -o $raw_singlecellexperiment_object    
        echo "status = ${status}"
        echo "output = ${output}"
        
        [ "$status" -eq 0 ]
        [ -f  "$raw_singlecellexperiment_object" ]
    }

Only exported variables are available for use in the test (as well as Bats' own ones, such as '$status'). 

Things to note:

* We have a conditional use of Bats' 'skip' command. If we have specified that existing outputs should be re-used (via $use_existing_outputs), and if that output file exists, then then test will not run.
* 'run' is a Bats command which executes a command and stores its return code in '$status'. 
* The 'echo' elements make sure that we see the full output of any errors- which are otherwise stashed away by Bats.
* The final bracketed elements are lists of assertions. We're saying that we should not have a non-zero return code, and the output file should exist once the commmand has run. 

Travis CI integration
---------------------

You may find it useful to integrate Travis CI to test scripts automatically during development. We have found a .travis.yml file like the following to be useful:

.. code-block:: yaml

    before_install:
      - if test -e $HOME/miniconda/bin; then
          echo "miniconda already installed.";
        else
          if [[ "$TRAVIS_PYTHON_VERSION" == "2.7" ]]; then
            wget https://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh -O miniconda.sh;
          else
            wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh;
          fi

          rm -rf $HOME/miniconda;
          bash miniconda.sh -b -p $HOME/miniconda;
          export PATH="$HOME/miniconda/bin:$PATH";
          hash -r;
          conda config --set always_yes yes --set changeps1 no;
          conda update -q conda;

          conda info -a;

          conda config --add channels defaults;
          conda config --add channels conda-forge;
          conda config --add channels bioconda;

          conda create -q -n test-environment python=$TRAVIS_PYTHON_VERSION r-base r-optparse libpng r-cairo r-workflowscriptscommon bioconductor-singlecellexperiment;
        fi

    install:
      - export PATH="$HOME/miniconda/bin:$PATH";
      - source activate test-environment

    before_script:
      - export PATH=`pwd`:$PATH

    script: ./bioconductor-singlecellexperiment-scripts-post-install-tests.sh

    cache:
      directories:
        - $HOME/miniconda
        - post_install_tests

      before_cache:
        - if ! [[ $TRAVIS_TAG ]]; then rm -rf $HOME/miniconda/conda-bld; fi
        - rm -rf post_install_tests/outputs
        - rm -rf $HOME/miniconda/locks $HOME/miniconda/pkgs $HOME/miniconda/var $HOME/miniconda/conda-meta/history

This sets up a Conda environment and exectutes the main test script for the package. Note that the conda evironment is cached, as is the directory containing test data, to prevent that data being re-downloaded on every build.
