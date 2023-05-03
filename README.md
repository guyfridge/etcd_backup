# etcd_backup

## Problem Statement
In this project, I will backup an etcd to a file called /tmp/myback and create a namespace called cep-project2 with a network policy that allows all the Pods in the same namespace to access one another.

## Steps to Take
1. Backing up the etcd cluster data
2. Creating and verifying the namespaces
3. Generating a certificate and private key in the worker node
4. Upgrading the Kubernetes cluster with the latest version

## Backing Up the ETCD Cluster Data

```
export RELEASE="3.3.13"
wget https://github.com/etcd-io/etcd/releases/download/v${RELEASE}/etcd-v${RELEASE}-linux-amd64.tar.gz
tar xvf etcd-v${RELEASE}-linux-amd64.tar.gz
cd etcd-v${RELEASE}-linux-amd64
sudo mv etcdctl /usr/local/bin

```

