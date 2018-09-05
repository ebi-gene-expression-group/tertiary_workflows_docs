###########################################
Adding scripts to existing wrapper packages
###########################################

The wrapper script packages `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_, `bioconductor-singlecellexperiment-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>`_ and `bioconductor-scater-scripts <https://github.com/ebi-gene-expression-group/bioconductor-singlecellexperiment-scripts>`_ have initial versions complete and available from Bioconda. 

If there's a function in one of the associated R packages (e.g. Seurat) that doesn't yet have a wrapper, you can write one yourself. Follow the guidelines at e.g. :doc:`scripts_for_r_packages`, and submit a PR e.g. to `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_. Once the update has been applied there and is part of a tagged release, it would also be helpful if you could update the associated Bioconda recipe (e.g. `this one <https://github.com/bioconda/bioconda-recipes/tree/master/recipes/r-seurat-scripts>` for Seurat scripts). The meta.yaml will need to be updated to point at the new release, and a test will need to be added for your new wrapper. Make those changes and submit via a PR.
