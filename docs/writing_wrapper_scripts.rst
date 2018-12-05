###############################
Writing toolkit wrapper scripts
###############################

Tools such as Seurat and Scanpy are libraries which provide functions for use within particular environents (R and Python respectively for these examples), but these functions are not available directly from the command line. In this case we need a wrapper which parses inputs and paramters, runs the specified functions, and outputs results to specfied locations. 

Clearly bioinformaticians everywhere are writing such wrapper scripts routinely. However we propose that wrappers are written in a way that makes them re-usable across a range of platforms and applications, using the following strategy:

.. toctree::
    :maxdepth: 2
   
    scripts_for_r_packages
