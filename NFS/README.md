# Dynamic NFS provisioning

see https://medium.com/@myte/kubernetes-nfs-and-dynamic-nfs-provisioning-97e2afb8b4a9
see https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client
see https://linuxacademy.com/guide/12824-getting-started-with-nfs-part-one/

First lets install NFS server on the host machine, and create a directory where our NFS server will serve the files:

1. Master Node Configuration

SSH to master node

```console 
vagrant ssh k8m-m-1
```

Install nfs server

```console 
sudo apt install  nfs-server
```

Create share folder and edit permision

```console 
sudo mkdir -p /srv/nfs/home
sudo chmod 777 -R /srv/nfs/home
```

Edit exports

```console 
sudo vim /etc/exports
```
Insert i to enable edit. And add two lines to file

/srv/nfs/home      192.168.50.12(rw,sync,no_subtree_check) // worker node 1
/srv/nfs/home      192.168.50.13(rw,sync,no_subtree_check) // worker node 2

Press escape and :wq to write and quit.

Make sure nfs server is active and running. 

```console 
 sudo systemctl status nfs-server
```
Note: if status is active(excited) it also working :) 

Make sure rpcbind is active and running. 

```console 
 sudo systemctl status rpcbind
```

2. Worker Node Configuration

Create folder to mount to server shared folder

 ```console 
  mkdir  /mnt/sharedir
```

Mount 

 ```console 
  sudo mount  192.168.50.11:/srv/nfs/home  /mnt/sharedir
```

Note: If mount command fails wait some time, it will work.


3. Deployment

Navigate to deploy folder

Service account must be set 

 ```console 
  kubectl apply -f rbac.yaml
```

Create an NFS persistence volume storage class

 ```console 
  kubectl apply -f class.yaml 
```

The NFS client provisioner will be set as a pod

 ```console 
  kubectl apply -f deployment.yaml
```
For Testing

Creating persistence volume claim file and deploying it

 ```console 
  kubectl apply -f claim.yaml
```

Navigate to master nodes /srv/nfs/home new folder should be crated
