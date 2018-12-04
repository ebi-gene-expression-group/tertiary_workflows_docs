###########################################
Galaxy wrapper generation with Planemo
###########################################

The tool we use to facilitate Galaxy wrapper generation is Planemo. For more information on the following refer to https://planemo.readthedocs.io/en/latest/readme.html.

**************************
Prerequisites
**************************

pip and virtualenv
==================

Before installing planemo latest version of pip 7.0 or greater and virtualenv-1.9 or greater. To install virtualenv globally with pip (if you have pip 1.3 or greater installed globally):

.. code-block:: bash

    [sudo] pip install virtualenv

Refer to user documents for further information https://virtualenv.pypa.io/en/stable/installation/

planemo
=======

Setting up virtual environment Installation of planemo

.. code-block:: bash

    virtualenv .venv; . .venv/bin/activate
    pip install "pip>=7" # Upgrade pip if needed.
    pip install planemo
 
Installing bioconda packages
============================

Developing galaxy tools wrappers for a particular bioconda recipe requires installation of the package in the same environment.  

For example, installing bioconda scater scripts package can be done like:

.. code-block:: bash

    conda install bioconductor-scater-scripts

**************************
Writing wrappers
**************************

For full instructions on this refer to Planemo's documentation itself at https://planemo.readthedocs.io/en/latest/writing_appliance.html. Here we will summarise usage for our use case.

The galaxy wrapper essentially includes XML files providing set of instruction to input and output files and files format that are used in bioconda recipes for an associate function. The planemo command `tool_init` takes various arguments and generates the skeletal structure of these XML files. Although XML can be written in a text editor planemo comands makes the process quicker.

An example demonstration of making a galaxy wrapper for a script, _`scater-read-10x-results`: https://github.com/ebi-gene-expression-group/bioconductor-scater-scripts/blob/ed456544658a17fe58d69ad06b9f88e78ba53c40/scater-read-10x-results.R, which reads 10X data, creates a SingleCellExperiemnt object and saves it in rds (rdata) format. This script is basically an interface to the function `read10xResults()` from Scater. **Note:** this function is now deprecated in Scater with functionality moved to DropletUtils(), we will update this example in due course.  

Before executing the planamo tools, the input test data used in the function to be located within the same environment. For brevity, create a folder with name of interest and store the test data and execute planmo tools within that folder.

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


To understand in depth the optional flags are discussed in depth in https://planemo.readthedocs.io/en/latest/writing_appliance.html. But the two most basic ones are `--id` and `--name` which indicates short identifier used by galaxy and short description of the tool respectively. Executing this planemo command will generate scater-read-10x-results.xml, macros.xml and folder test-data and copy of tests data within that folder.

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

* Scater-read-10x-results.xml will have have generic input and input variable names, renamed here for clarity
* The format of rds was renamed to `rdata` as it widely accepted within galaxy community 
* Symlinks were created to point input variable names 
* It is recommended to use the version of bioconductor package used in bioconda to be in `tool id` version section for consistancy. For example running the above command will default use the version 0.1.0, this needs to be amended to reflect the actual version of the underlying software. In the approach we use here the wrappers called (e.g. from the bioconductor-scater-scripts package) are a thin syntax layer around the tool itself (e.g. Scater), and the version should reflect that of the tool itself, not that of the wrapper. 
* The preferred version format is "wrapped.software.version+galaxy.wrapper.version", for example "0.0.3+galaxy0". When multiple xml wrap around the same software and therefore share the same software version, it can be replaced by a token that is defined in macros.xml, for example "@TOOL_VERSION@" and the version of each wrapper looks like "@TOOL_VERSION@+galaxy0". The optional help section in `[CDATA[` describing the options flag function that is associated with input data needs to be moved to input <param section for clarity in galaxy optional usage.

Macros
======

macros.xml will help reduce the redundant information in the galaxy wrappers which are repeated. For instance, the version of R used or bioconductor scater packages and a reference to citation and url to github repository.   

The optional flag `--macros` to Planemo will produce two xml files in current directory. Although it will be named macros.xml by default it's renamed here to scater_macros.xml and used the revised name pointing to the same name in Scater-read-10x-results.xml.

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


More information on galaxy wrapper xml schema can be found at https://docs.galaxyproject.org/en/latest/dev/schema.html and best practices for devlopment can be found at https://galaxy-iuc-standards.readthedocs.io/en/latest/best_practices.html.

Linting
=======

In order the validate or check for sanity of xml generated planamo provides `lint` command to review the tool and identifies if all checks are OK. The output will look something like this

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


Furthermore one can test for execution of R wrapper and looks for the output through the command. 

.. code-block:: bash
    Planemo test 
    All 1 test(s) successfully executed.

This will create symlinks and use the input data provided within test environment and execute in galaxy server.

