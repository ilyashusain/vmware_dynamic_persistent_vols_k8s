# vmware_persistent_vols_k8s

## Prerequisites:

- A kubernetes cluster (v1.12) running on vsphere.

## Brief:

In this guide we will go over how to attain persistent storage for our containerized k8s deployments, in particular a jenkins container instance. To achieve this, we will persist the ```/var/jenkins_home``` directory on the container's OS.

## 1. Create the storage classes and pvcs

Execute the following commands to create the storage class and associated pvc:

```kubectl apply -f storage-class.yml```

```kubectl apply -f persistent-volume-claim.yml```
