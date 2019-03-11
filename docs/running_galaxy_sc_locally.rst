############################################################
Running Hinxton Single Cell Interactive Analysis Environment
############################################################

Hinxton Single Cell is Galaxy setup with tools tertiary analysis of single cell data.
Galaxy is an open, web-based platform for accessible, reproducible, and
transparent computational biomedical research.

Within this project, we have created a Galaxy flavour (a Galaxy setup with
defined tools and workflows) for the tertiary analysis of single cell RNA-Seq data.

This guide shows you how to run the Galaxy Single Cell Tertiary analysis setup
on your own machine using Minikube. The setup relies on the Kubernetes container
orchestrator.

************
Requirements
************

- Helm installed: Please follow `official instructions for installing Helm <https://github.com/helm/helm/blob/master/docs/install.md#installing-the-helm-client>`_.
- Access to a Kubernetes cluster (with shared file system accessible through a Persistent Volume or equivalent).
  - For development purposes or local tests, the local Minikube environment can be used. Install minikube following `official instructions for minikube <https://kubernetes.io/docs/tasks/tools/install-minikube/>`_.
- kubectl cli: The command line argument for connection to a Kubernetes instance (remote cluster or local minikube). If not installed as part of Minikube steps, follow ONLY the installation steps (not the configuration ones) from `here <https://kubernetes.io/docs/tasks/tools/install-kubectl/>`_).

________
Minikube
________

If using minikube, you need to make sure that it is running. If you just
installed it, you need to execute ``minikube start``. In general you can check
the status of minikube through ``minikube status``. For a smoother run, you might want to assign more RAM to Minikube.

***********************
First time installation
***********************

If using helm for the first time, you will need to initialise helm on the cluster and add the helm repo to the local helm directories:

.. code-block:: bash

  helm init --wait
   helm repo add galaxy-helm-repo https://pcm32.github.io/galaxy-helm-charts


if you have done this once in the past, you might need, from time to time, to update the local repo, by doing:

.. code-block:: bash

  helm repo update

*****************************************
Retrieve and customise configuration file
*****************************************

The galaxy-stable Helm chart that we will use allows to configure most Galaxy
settings. For that, get the `sample config file <https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary/blob/1.0.0/helm-configs/hinxton-singlecell-1.0.0_g18.05-minikube.yaml>`_.

Edit or make a copy of this file based on your needs and the
`galax-stable chart documentation`__. However, if you only need to run on
Minikube, the sample config file should do fine. You might want to set a user
and password there.

.. _ChartDocs: https://github.com/galaxyproject/galaxy-kubernetes/blob/develop/README-galaxy-stable.md#variables
__ ChartDocs_

**********
Normal run
**********

Now the only step needed is to initialize Galaxy through the helm chart just added.
For this execute:

.. code-block:: bash

   helm install -f hinxton-singlecell-1.0.0_g18.05-minikube.yaml \
     --version 2.0.2 galaxy-helm-repo/galaxy-stable

*************
Access Galaxy
*************

To access your local Galaxy setup you need to find out the ip from minikube:

.. code-block:: bash

   minikube ip

Usually the ip number will be ``192.168.99.100`` (but confirm with the above call).
Then access on that ip port 30700 with the galaxy prefix ``http://192.168.99.100:30700/``.

If you are not using minikube, the Galaxy instance will be on port 30700 of any of the
node's IPs of the k8s cluster (unless that you had setup the ingress).
