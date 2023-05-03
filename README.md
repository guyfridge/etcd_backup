# etcd_backup
Make a backup of the etcd database in a file called /tmp/myback

# Problem Statement
As an infrastructure admin, you need to take the backup of an etcd in a file called /tmp/myback. Make sure to have a namespace called cep-project2 with a network policy configured in such a way that all the Pods in the same namespace should access each other. Any other Pods from the non cep-project2 should not access the Pods. Configure a Kubernetes client on worker node 3 in such a way that user4 should have only view access to cep-project2. Update the master with the latest version of the Kubernetes.
