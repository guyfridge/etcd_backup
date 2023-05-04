# etcd_backup

## Problem Statement
In this project, I will backup an etcd to a file called /tmp/myback and create a namespace called cep-project2 with a network policy that allows all the Pods in the same namespace to access one another.

## Steps to Take
1. Backing up the etcd cluster data
2. Creating and verifying the namespaces
3. Create a network policy that only allows for communication between pods in the designated namespace
4. Generating a certificate and private key in the worker node
5. Upgrading the Kubernetes cluster with the latest version

## Backing Up the ETCD Cluster Data

### Install etcdctl:
```
export RELEASE="3.3.13"
wget https://github.com/etcd-io/etcd/releases/download/v${RELEASE}/etcd-v${RELEASE}-linux-amd64.tar.gz
tar xvf etcd-v${RELEASE}-linux-amd64.tar.gz
cd etcd-v${RELEASE}-linux-amd64
sudo mv etcdctl /usr/local/bin
```
### Verify installation:
```
guyfridge@instance-group-1-09jz:~$ etcdctl --version
etcdctl version: 3.3.13
API version: 2
```
### Save a snapshot of the etcd to /tmp/myback
```
guyfridge@instance-group-1-09jz:~$ hostname -i
10.138.0.4
guyfridge@instance-group-1-09jz:~$ ETCDCTL_API=3 etcdctl --endpoints=10.138.0.4:2379 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key snapshot save /tmp/myback
Error: open /etc/kubernetes/pki/etcd/server.key: permission denied
guyfridge@instance-group-1-09jz:~$ sudo ETCDCTL_API=3 etcdctl --endpoints=10.138.0.4:2379 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key snapshot save /tmp/myback
Snapshot saved at /tmp/myback
```
## Creating and Verifying the Namespaces
### Create a namespace called cep-project2
```
vi project2-ns.yaml
```
- Press 'i'
- Enter the following
```
kind: Namespace
apiVersion: v1
metadata:
  name: cep-project2
  labels:
    slearn: cep-project2
```
## Create a network policy that only allows for communication between pods in the designated namespace
- Add the following to project2-ns.yaml
```
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: allow-traffic-in-ns
 namespace: cep-project2
spec:
 podSelector: {}
 policyTypes:
  - Ingress
 ingress:
  - from:
     - namespaceSelector:
         matchLabels:
           slearn: cep-project2
```
- Press 'esc' + ':wq' to save and quit the YAML file
## Generate a Certificate and Private Key for Authentication as user4
- From the master node
```
sudo vi /etc/ssl/openssl.cnf
```
- Press 'i'
- Comment out the following line
```
RANDFILE = $ENV::HOME/.rnd
```
- Press 'esc' + ':wq' to save and quit
- Continue with the following on the master node
```
sudo mkdir -p /home/certs
cd /home/certs
sudo openssl genrsa -out user4.key 2048
sudo openssl req -new -key user4.key -out user4.csr -subj "/CN=user4/O=devops"
sudo openssl x509 -req -in user4.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out user4.crt -days 1000 ; ls -ltr
```
### Execute project2-ns.yaml to create the namespace 'cep-project2' and initialize the network policy
```
kubectl apply -f project2-ns.yaml
```
### Provide read-only access to user4 on the cep-project2 namespace
```
sudo kubectl --kubeconfig=/etc/kubernetes/admin.conf create role readonly --namespace=cep-project2 --verb=get,list,watch --resource="*.*"
sudo kubectl --kubeconfig=/etc/kubernetes/admin.conf create rolebinding user4-access --namespace=cep-project2 --user=user4 --role=readonly
```

