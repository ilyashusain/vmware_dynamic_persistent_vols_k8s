# vmware_persistent_vols_k8s

## Prerequisites:

- A kubernetes cluster (v1.12) on vsphere.

## Brief:

In this guide we will go over how to attain persistent storage for our containerized k8s deployments, in particular a jenkins container. To achieve this, we will persist the ```/var/jenkins_home``` directory on the container's OS.
