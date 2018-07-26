##################################
Running Galaxy SC Tertiary locally
##################################

This guide shows you how to run the Galaxy Single Cell Tertiary analysis setup
on your own machine. The setup relies on the Kubernetes container orchestrator.

************
Requirements
************

Please follow the "Requirements", "Minikube" and "First time installation"
sections from the Galaxy helm chart site at
https://github.com/galaxyproject/galaxy-kubernetes/blob/develop/README.md


*****************************************
Retrieve and costumize configuration file
*****************************************

The galaxy-stable Helm chart that we will use allows to configure most Galaxy
settings. For that, get the `sample config file`__.

.. _ConfigSample: https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary/blob/develop/helm-configs/tertiary-portals-galaxy-18.05-minikube.yaml

__ ConfigSample_

Edit or make a copy of this file based on your needs and the
`galax-stable chart documentation`__. However, if you only need to run on
Minikube, the sample config file should do fine. You might want to set a user
and password there.

.. _ChartDocs: https://github.com/galaxyproject/galaxy-kubernetes/blob/develop/README-galaxy-stable.md#variables
__ ChartDocs_

**********
Normal run
**********

Now the only step needed is to initialize Galaxy through the helm chart just added. For this follow:

.. code-block:: bash

   helm install -f tertiary-portals-galaxy-18.05-minikube.yaml galaxy-helm-repo/galaxy-stable

*************
Access Galaxy
*************

To access your local Galaxy setup you need to find out the ip from minikube:

.. code-block:: bash

   minikube ip

Usually the ip number will be ``192.168.99.100`` (but confirm with the above call).
Then access on that ip port 30700 with the galaxy prefix ``http://192.168.99.100:30700/galaxy/``.
Don't forget the trailing ``/``.
