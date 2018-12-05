###########################################
Adding scripts to existing wrapper packages
###########################################

If a wrapper script package exists but does not contain a wrapper for functionality you need, please propose a new wrapper via a pull request. See :doc:`modules` for currently written or in-progress components.

Follow the guidelines at e.g. :doc:`scripts_for_r_packages`, and submit a PR e.g. to `r-seurat-scripts <https://github.com/ebi-gene-expression-group/r-seurat-scripts>`_. Note that the script won't be available via Bioconda until there's a new release including your script, and the Bioconda recipe has been updated to point to that new release, but this is also a process you can contribute to.
