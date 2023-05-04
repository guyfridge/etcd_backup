# etcd_backup

## Problem Statement
In this project, I will backup an etcd to a file called /tmp/myback and create a namespace called cep-project2 with a network policy that allows all the Pods in the same namespace to access one another.

## Steps to Take
1. Backing up the etcd cluster data
2. Creating and verifying the namespaces
3. Generating a certificate and private key in the worker node
4. Upgrading the Kubernetes cluster with the latest version

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
