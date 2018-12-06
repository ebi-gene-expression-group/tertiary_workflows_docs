################
Project overview
################

Single-cell tertiary analyses include step such as:

* expression matrix normalisation
* cell filtering
* computation of dimension reductions
* clustering itself
  

These steps may be implemented in a variety of ways including stand-alone tools, scripts, or R package functions. To compare equivalent logical steps between workflows, and to 'mix and match' those components for optimal workflows is therefore a challenging excercise without additional infrastructure.

We aim to 'componentise' tools associated with these workflows and more importantly build standards and infrastructure to facilitate this process for others in future.

**********
Objectives
**********

* Write additional scripts where necessary to expose inividual worklow units as scripts with well-defined inputs and outputs.
* Build (programming) language-independent standards for input, output and intermediate data types.
* Make components avialable in as simple a manner as possible, via the Bioconda repository.
* Make containers available for these packages, suitable for cloud deployment.
* Make preliminary assessments of individual workflows, components, and 'mixed' workflows. 
