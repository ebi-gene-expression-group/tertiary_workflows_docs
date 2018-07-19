################
Project overview
################

This project is concerned with building infrastructure for benchmarking components of tertiary (post-quantification) pipelines in single-cell RNA-seq analysis.

Pipelines for tertiary analyses such as clustering are built from individual components representing logical steps such as expression matrix normalisation, cell filtering, computation of dimension reductions and clustering itself. These steps may be implemented in a variety of ways including stand-alone tools, scripts, or R package functions. To compare equivalent logical steps between workflows, and to 'mix and match' those components for optimal workflows is therefore a challenging excercise without additional infrastructure.

We aim to 'componentise' tools associated with these workflows and more importantly build standards and infrastructure to facilitate this process for others in future.

**********
Objectives
**********

* Write additional scripts where necessary to expose inividual worklow units as scripts with well-defined inputs and outputs.
* Build (programming) language-independent standards for input, output and intermediate data types.
* Make components avialable in as simple a manner as possible, via the Bioconda repository.
* Make preliminary assessments of individual workflows, components, and 'mixed' workflows. 

**************
Implementation
**************

Components will be made available via Bioconda packages associated with those of the source package. For example we will provide scripts to run individual steps of the 'Seurat' package by providing an 'r-seurat-scripts' package associated the existing 'r-seurat' package. The components thus provided can be chained together in common workflow systems (Nextflow, Snakemake, WDL) and we will provide workflows implemented in thise way. 

Since Bioconda packages are 'containerised' automatically to produce containters in the Biocontainers repository, this will also have the effect of providing components for cloud-based workflows. We will leverage these to build cloud-based workflows, for example using Galaxy.
