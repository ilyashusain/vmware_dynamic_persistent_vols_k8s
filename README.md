# vmware_dynamic_vols_k8s

## Prerequisites:

- A kubernetes cluster (v1.12) running on vsphere.

*NOTE: If you are using a different provider, you will need to alter the storage class's provider.*

## Brief:

In this guide we will go over how to attain persistent storage for our containerized k8s deployments, in particular a jenkins container instance. To achieve this, we will persist the jenkins home directory ```/var/jenkins_home``` on the container's OS onto a persistent volume cluster resource.

## 1. Create the storage class and pvc:

Execute the following commands to create the storage class and associated pvc (persistent volume claim):

```kubectl apply -f storage-class.yml```

```kubectl apply -f persistent-volume-claim.yml```

As we invoke the storage class utility, and apply a persistent volume claim, a persistent volume hosting 15Gi of space (specified by us in the pvc) is dynamically provisioned and it is this volume where the jenkins home folder will be persisted. To view the persistent volume you just created, run ```kubectl get pv```. You can also view the pv in the vspehre ui in the storage directories for your datacenter at /DC1/kube-vols.

## 2. Change mounting location of jenkins.yml

To begin persisting the jenkins folder, set the mountPath in the jenkins.yaml to "/etc/testing" as follows:

  ...
        volumeMounts:
        - name: jobs
          mountPath: "/var/loading"
  ...
  
Any data to be persisted to the volume will now be located in this directory.

## 3. Create the jenkins deployment:

Execute:

```kubectl apply -f jenkins.yml```

to create the jenkins master deployment. Locate which worker node your jenkins pod was deployed to by getting the name of your pod with ```kubectl get pods```, then take the name of the pod and run ```kubectl describe pods <pod name>``` to find the node that hosts the deployment.

## 4. ssh into jenkins container as root

First, ssh into the node that hosts the jenkins deployment as uncovered in previous step:

	ssh <worker node ip where jenkins deployed>

Then list the running containers and search for your jenkins container:

	sudo docker ps -a | head -6 #search for ID of jenkins container
  
Finally, ssh into this container:

	sudo docker exec -it -u root <docker ID of jenkins-auto-ci> /bin/bash
  
## 5. Import jenkins home directory onto volume

Copy the contents of `/var/jenkins_home` onto the volume's directory which we specified as `/etc/loading` by executing `cp -R /var/jenkins_home/. /etc/testing`.

## 6. Set appropriate permissions on the persistent volume directory

`cd` into `/var/testing` and execute `ls -al`; you'll see that jenkins home directory has been copied in, but all files are owned by root (and not jenkins), this is problematic. To fix this, change the permissions on the `/var/testing` directory itself and all of its contents by executing within /var/testing `chown -R jenkins:jenkins . `; if this is not done jenkins will throw errors in the browser. Once this is done, check that all files are owned by jenkins by running `ls -al` again.

## 7. Reset the mount path in jenkins.yaml file

Finally, exit the container and change the mount path in the jenkins.yaml back to `/var/jenkins_home` as such:

  ...
        volumeMounts:
        - name: jobs
          mountPath: "/var/jenkins_home"
  ...

Apply the changes:

	kubectl apply -f jenkins.yaml
	
You have now persisted the jenkins home folder, and therefore you have effectively persisted the jenkins container.
