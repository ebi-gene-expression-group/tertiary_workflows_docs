###########################################
Adding scripts to existing wrapper packages
###########################################

The wrapper script packages `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_, `bioconductor-singlecellexperiment-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>`_ and `bioconductor-scater-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>`_ have initial versions complete and available from Bioconda. 

If there's a function in one of the associated R packages (e.g. Seurat) that doesn't yet have a wrapper, you can write one yourself. Follow the guidelines at e.g. :doc:`scripts_for_r_packages`, and submit a PR e.g. to `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_. Note that the script won't be available via Bioconda until there's a new release including your script, and the Bioconda recipe has been updated to point to that new release.
