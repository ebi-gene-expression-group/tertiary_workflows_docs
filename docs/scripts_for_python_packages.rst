###########################################
Writing wrapper scripts for Python packages
###########################################

********************************
Implementing as a python package
********************************

Here we use `scanpy-scripts <https://github.com/ebi-gene-expression-group/scanpy-scripts>`_ as an example.

Directory structure
-------------------

.. code-block::

    .
    ├── LICENSE
    ├── README.md
    ├── scanpy-scripts-tests.bats  # tests for cli commands using bats
    ├── scanpy_scripts
    │   ├── __init__.py
    │   ├── cli.py                 # defines cli commands and provides entry points
    │   ├── click_utils.py         # defines utility classes and functions for click-based cli handling
    │   ├── cmd_options.py         # defines sub-command (function) options
    │   ├── cmd_utils.py           # defines utility functions for cmds.py
    │   ├── cmds.py                # defines functions for each sub-commands
    │   ├── ...
    │   ├── lib                    # implementation of each wrapper functions
    │   │   ├── __init__.py
    │   │   ├── _diffexp.py
    │   │   ├── ...
    │   │   └── _umap.py
    │   └── obj_utils.py           # defines common utility functions for handling anndata object
    └── setup.py                   # package metadata and entry points
