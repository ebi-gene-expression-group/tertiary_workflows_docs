*Galaxy wrapper generation with Planemo*


**************************
Prerequisites
**************************
Installation of 
-  pip
- virtualenv
- planmeo

Before installing planemo it is prerequisite that you have latest version of pip 7.0 or greater and virtualenv-1.9 or greater

To install globally with pip (if you have pip 1.3 or greater installed globally):

.. code-block:: bash

[sudo] pip install virtualenv

Refer to user documents for further information https://virtualenv.pypa.io/en/stable/installation/

Setting up virtual environment Installation of planemo

.. code-block:: bash

$ virtualenv .venv; . .venv/bin/activate
$ pip install "pip>=7" # Upgrade pip if needed.
$ pip install planemo
 
https://planemo.readthedocs.io/en/latest/readme.html#obtaining

After installation you can test if planemo


Installing bioconda packages

Developing galaxy tools wrappers for a particular bioconda recipe requires installation of the package in the same environment.  

For example, installing bioconda scater package.

.. code-block:: bash

conda install bioconductor-scater-scripts

Setup executables pointing to local installed minicoda paths.	

**************************
The basics
**************************

The galaxy wrapper essentially includes XML files providing set of instruction to input and output files and files format that are used in bioconda recipes for an associate function.

This documents provides brief documentation of usage of planemo tool for generating XML files  https://planemo.readthedocs.io/en/latest/writing_appliance.html. The planemo has command `tool_init` that takes various arguments that generates the skeletal structure of XML files. Although XML can be written in a text editor planemo comands makes the process quicker.

An example demonstration of making a galaxy wrapper for a function, read_10X from bioconductor-scater-scripts package and saving in rds (rdata) format. Before executing the planamo tools, the input test data used in the function to be located within the same environment. For brevity, create a folder with name of interest and store the tests data and execute planmo tools within that folder.

`Tests data`
barcodes.tsv
genes.tsv
matrix.mtx


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



To understand in depth the optional flags are discussed in depth in 
https://planemo.readthedocs.io/en/latest/writing_appliance.html. But the two most basic ons are --id and --name which indicates short identifier used by galaxy and short description of the tool respectively.

Executing the above planemo commands will generate scater-read-10x-results.xml, macros.xml and folder test-data and copy of tests data within that folder.


Scater-read-10x-results.xml will have have generic input and input variable names. I’ve renamed for clarity. In addition, format of rds was renamed to `rdata` as it widely accepted within galaxy community. Symlinks were created to point input variable names. It is also recommended to use the version of bioconductor package used in bioconda to be in `tool id` version section for consistancy. For example running the above command will default use the version 0.1.0, this needs to be amende to "0.0.3" with what bioconda supports. The optional help section in `[CDATA[` describing the options flag function that is associated with input data needs to be moved to input <param section for clarity in galaxy optional usage.

<tool id="scater-read-10x-results" name="Scater read 10x data" version="0.0.3">
    <description>Loads 10x data into a serialized scater R object</description>
    <macros>
        <import>scater_macros.xml</import>
    </macros>
    <expand macro="requirements" />
    <command detect_errors="exit_code"><![CDATA[
        ln -s '$matrix' matrix.mtx;
        ln -s '$genes' genes.tsv;
        ln -s '$barcodes' barcodes.tsv;

        scater-read-10x-results.R -d ./ -o '$R_scater_serialized'
    ]]></command>
    <inputs>
        <param type="data" name="matrix" format="mtx" />
        <param type="data" name="genes" format="tabular" />
        <param type="data" name="barcodes" format="tabular" />
    </inputs>
    <outputs>
        <data name="R_scater_serialized" format="rdata" label="" />
    </outputs>
    <tests>
        <test>
            <param name="matrix" value="matrix.mtx"/>
            <param name="genes" value="genes.tsv"/>
            <param name="barcodes" value="barcodes.tsv"/>
            <output name="R_scater_serialized" file="R_scater_serialized.rds"/>
        </test>
    </tests>
    <help><![CDATA[
        Usage: scater-read-10x-results.R [options]


Options:
	-d DATA-DIR, --data-dir=DATA-DIR
		Directory containing the matrix.mtx, genes.tsv, and barcodes.tsv files provided by 10X. A vector or named vector can be given in order to load several data directories. If a named vector is given, the cell barcode names will be prefixed with the name.

	-o OUTPUT-OBJECT-FILE, --output-object-file=OUTPUT-OBJECT-FILE
		File name in which to store serialized R matrix object.

	-h, --help
		Show this help message and exit



    ]]></help>
    <expand macro="citations" />
</tool>


macros.xml will help reduce the redundant information in the galaxy wrappers which are repeated. For instance, the version of R used or bioconductor scater packages. In addition, reference to citation and url to github repository.   

Optional flag `--macros` will produce two xml files in current directory. Although it will name macros.xml by default I’ve renamed to scater_macros.xml and used the revised name pointing to the same name in Scater-read-10x-results.xml.

 

xml block in scater-read-10x-results.xml

.. code-block:: XML

<macros>
        <import>scater_macros.xml</import>
 </macros>

Scater_macros.xml

.. code-block:: XML

<macros>
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

