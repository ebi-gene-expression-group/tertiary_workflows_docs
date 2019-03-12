###########################################
Galaxy wrappers
###########################################

`Galaxy <https://galaxyproject.org/>`_ is a popular tool for writing and running workflows. Tools are given Galaxy wrappers which then provide a web-based user interface for that tool with outputs of defined types which can be passed to other tools. 

***********************
Writing Galaxy wrappers
***********************

Galaxy wrappers are just XML files, but writing them well can be tricky. The tool we recommend to facilitate Galaxy wrapper generation is Planemo, and we introduce basic instructions for its operation here. For more information on the following refer to https://planemo.readthedocs.io/en/latest/readme.html.

Prerequisites
==================

pip and virtualenv
------------------

Before installing Planemo, install the latest version of pip 7.0 or greater and virtualenv-1.9 or greater. To install virtualenv globally with pip (if you have pip 1.3 or greater installed globally):

.. code-block:: bash

    [sudo] pip install virtualenv

Refer to user documents for further information https://virtualenv.pypa.io/en/stable/installation/

Planemo
-------

Setting up virtual environment Installation of Planemo

.. code-block:: bash

    virtualenv .venv
    . .venv/bin/activate
    pip install "pip>=7" # Upgrade pip if needed.
    pip install planemo

Install problems (probably temporary)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We are currently experiencing blank white screens with Galaxy installs, due to the client parts of Galaxy not being installed correctly. This occurs with the Galaxy installed by Planemo. To address the problem you need to install Galaxy separately and make a couple of tweaks:

* Download the latest Galaxy in a separate directory and do the the 'run.sh' step.
* You may see an error like "Cannot uninstall 'certifi'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall." In this case update the certifi entries to certifi==2018.10.15 in lib/galaxy/dependencies/dev-requirements.txt and requirements.txt and try again.
* You may well see install problems with nodejs. In this case you need to activate the conda environment used by Galaxy and downgrade nodejs to a 9* version (the latest and default version installed is 10*)

Provided the Galaxy install gets most of the way through such that the client is in place, you can then point at this Galaxy install from Planemo with commands like the following (see below):

.. code-block:: bash

    planemo serve --galaxy_root ../galaxy


Installing bioconda packages
----------------------------

Developing galaxy tools wrappers for a particular bioconda recipe requires installation of the package in the same environment.  

For example, installing bioconda scater scripts package can be done like:

.. code-block:: bash

    conda install bioconductor-scater-scripts

Writing wrapper XML
====================

For full instructions on this refer to Planemo's documentation itself at https://planemo.readthedocs.io/en/latest/writing_appliance.html. Here we will summarise usage for our use case.

Galaxy wrappers are essentially XML files providing a set of instructions for input and output files and their format, which can then be passed to a command for execution. The Planemo command :code:`tool_init` takes various arguments and generates the skeletal structure of these XML files. Although XML can be written in a text editor Planemo commands makes the process quicker.

For example, we generated a wrapper for `scater-read-10x-results.R <https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts/blob/ed456544658a17fe58d69ad06b9f88e78ba53c40/scater-read-10x-results.R>`_ (this script is now obsolte due to changes in Scater, but the example still serves). This script reads 10X data, creates a SingleCellExperiemnt object from 10X format data, by calling :code:`read10xResults()` from Scater, and saves it in a serialized R object. 

Before executing the planamo tools, input test data used in the function must be located within the same environment. Create a folder to store the test data and execute planmo tools within that folder.

Test data:

* barcodes.tsv
* genes.tsv
* matrix.mtx

.. code-block:: xml

    planemo tool_init   --force \
        --macros \
        --id 'scater-read-10x-results' \
        --description 'Loads 10x data into a serialized scater R object' \
        --name 'Scater read 10x data' \
        --requirement bioconductor-scater-scripts@0.0.3 \
        --example_command 'scater-read-10x-results.R -d DATA-DIR -o OUTPUT-OBJECT-FILE' \
        --example_input matrix.txt \
        --example_input genes.tsv \
        --example_input barcodes.tsv \
        --example_output R_scater_serialized.rds \
        --test_case \
        --cite_url 'https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts' \
        --help_from_command 'scater-read-10x-results.R -h'


The optional flags are discussed in depth in https://planemo.readthedocs.io/en/latest/writing_appliance.html. But the two most basic ones are :code:`--id` and :code:`--name` which indicate the identifier used by galaxy and a short description of the tool, respectively. Executing this Planemo command will generate :code:`scater-read-10x-results.xml`, :code:`macros.xml` and folder :code:`test-data` and copy of tests data within that folder.

.. code-block:: xml

    <tool id="scater-read-10x-results" name="Scater read 10x data" version="@TOOL_VERSION@+galaxy0">
    <description>Loads 10x data into a serialized scater R object</description>
    <macros>
        <import>scater_macros.xml</import>
    </macros>
    <expand macro="requirements" />
    <command detect_errors="exit_code"><![CDATA[
        ln -s '$matrix' matrix.mtx &&
        ln -s '$genes' genes.tsv &&
        ln -s '$barcodes' barcodes.tsv &&

        scater-read-10x-results.R -d ./ -o '$R_scater_serialized'
    ]]></command>
    <inputs>
        <param type="data" name="matrix" format="txt" label="Expression quantification matrix in sparse matrix format (.mtx)"/>
        <param type="data" name="genes" format="tabular" label="Gene table"/>
        <param type="data" name="barcodes" format="tabular" label="Barcode/Cell table"/>
    </inputs>
    <outputs>
        <data name="R_scater_serialized" format="rdata" label="${tool.name} on ${on_string}: ${output_format}"/>
    </outputs>
    <tests>
        <test>
            <param name="matrix" value="matrix.mtx"/>
            <param name="genes" value="genes.tsv"/>
            <param name="barcodes" value="barcodes.tsv"/>
            <output name="R_scater_serialized" file="R_scater_serialized.rds" ftype="rdata" compare="sim_size"/>
        </test>
    </tests>
    <help><![CDATA[

    scater-read-10x-results.R

    This is a galaxy interface to scater function read10XResults()

    For more information check https://www.bioconductor.org/packages/release/bioc/html/scater.html

        ]]></help>
        <expand macro="citations" />
    </tool>

Note:

* :code:`Scater-read-10x-results.xml` will have have generic input and input variable names, renamed here for clarity
* The format of rds was renamed to :code:`rdata` as it widely accepted within galaxy community 
* Symlinks were created to point input variable names 
* It's important to use the appropriate version string in the :code:`tool id` version section. 
  - Running the above command will by default use the version 0.1.0, this needs to be amended to reflect the actual version of the underlying software. 
  - In the Scater script example  above (and other components we have built), the wrapper is a thin syntax layer around the tool itself (e.g. Scater), and so the version should reflect that of the tool itself, not that of the wrapper. In this case we use the version of the Scater bioconductor package. 
  - The preferred version format is :code:`"wrapped.software.version+galaxy.wrapper.version"`, for example :code:`"0.0.3+galaxy0"`. When multiple xml file wrap around the same software and therefore share the same software version, it can be replaced by a token that is defined in macros.xml, for example :code:`"@TOOL_VERSION@"` and the version of each wrapper looks like :code:`"@TOOL_VERSION@+galaxy0"`. The optional help section in :code:`"CDATA[...]"` describing the options flag function that is associated with input data needs to be moved to :code:`"<input> <param .../> </input>"` section for clarity in galaxy optional usage.

Macros
------

macros.xml will help reduce the redundant information in the galaxy wrappers which are repeated. For instance, the version of R used or bioconductor packages and a reference to citation and url to github repository.   

The optional flag :code:`--macros` to Planemo will produce two xml files in current directory. Although it will be named macros.xml by default it's renamed here to scater_macros.xml and uses the revised name pointing to the same name in Scater-read-10x-results.xml.

Here is the xml block in scater-read-10x-results.xml

.. code-block:: XML

    <macros>
        <import>scater_macros.xml</import>
    </macros>

Here is scater_macros.xml

.. code-block:: XML

    <macros>
        <token name="@TOOL_VERSION@">1.6.0</token>
        <xml name="requirements">
            <requirements>
                <requirement type="package" version="0.0.3">bioconductor-scater-scripts</requirement>
                <yield/>
            </requirements>
        </xml>
        <xml name="version">
            <version_command><![CDATA[
                echo $(R --version | grep version | grep -v GNU)", scater version" $(R --vanilla --slave -e "library(scater); cat(sessionInfo()\$otherPkgs\$scater\$Version)" 2> /dev/null | grep -v -i "WARNING: ")
                ]]></version_command>
        </xml>
        <xml name="citations">
            <citations>
                <citation type="bibtex">
                    @misc{githubbioconductor-scater-scripts,
                        author = {LastTODO, FirstTODO},
                        year = {TODO},
                        title = {bioconductor-scater-scripts},
                        publisher = {GitHub},
                        journal = {GitHub repository},
                        url = {https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts},
                }</citation>
             <yield />
             </citations>
        </xml>
    </macros>


More information on galaxy wrapper xml schema can be found at https://docs.galaxyproject.org/en/latest/dev/schema.html and best practices for development can be found at https://galaxy-iuc-standards.readthedocs.io/en/latest/best_practices.html.

Linting
-------

In order to validate or sanity check the generated XML, Planemo provides the :code:`lint` command to review the tool. The output will look something like this:

.. code-block:: bash

    planemo l
    Linting tool /galaxy_wrapper/scater/read-10x/scater-read-10x-results.xml
    Applying linter tests... CHECK
    .. CHECK: 1 test(s) found.
    Applying linter output... CHECK
    .. INFO: 1 outputs found.
    Applying linter inputs... CHECK
    .. INFO: Found 3 input parameters.
    Applying linter help... CHECK
    .. CHECK: Tool contains help section.
    .. CHECK: Help contains valid reStructuredText.
    Applying linter general... CHECK
    .. CHECK: Tool defines a version [0.1.0].
    .. CHECK: Tool defines a name [Scater read 10x data].
    .. CHECK: Tool defines an id [scater-read-10x-results].
    .. CHECK: Tool targets 16.01 Galaxy profile.
    Applying linter command... CHECK
    .. INFO: Tool contains a command.
    Applying linter citations... CHECK
    .. CHECK: Found 1 likely valid citations.


We can also test for execution of the R wrapper using the command:

.. code-block:: bash
    Planemo test 
    All 1 test(s) successfully executed.

This will create symlinks and use any provided test data to execute the tool in Galaxy.
