# vmware_dynamic_vols_k8s

## Prerequisites:

- A kubernetes cluster (v1.12) running on vsphere.

## Brief:

In this guide we will go over how to attain persistent storage for our containerized k8s deployments, in particular a jenkins container instance. To achieve this, we will persist the jenkins home directory ```/var/jenkins_home``` on the container's OS onto a persistent volume cluster resource.

## 1. Create the storage class and pvc:

Execute the following commands to create the storage class and associated pvc (persistent volume claim):

```kubectl apply -f storage-class.yml```

```kubectl apply -f persistent-volume-claim.yml```

As we invoke the storage class utility, and apply a persistent volume claim, a persistent volume hosting 15Gi of space (specified by us in the pvc) is dynamically provisioned and it is this volume where the jenkins home folder will be persisted. To view the persistent volume you just created, run ```kubectl get pv```. You can also view the pv in the vspehre ui in the storage directories for your datacenter at /DC1/kube-vols.
