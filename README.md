# AWS Kubernetes Bootstrap

This repo contains an Ansible playbook that can be executed from a remote host
to bootstrap a single master Kubernetes cluster with an arbitrary number of
worker nodes in AWS. 

### Dependencies

- Python on all hosts
- Ansible
- AWS VPC configured with NACL, Security Groups, and Subnets in place

### Running The Playbook

#### Variables

The variables for the playbook are stored in project_vars.yml. The only
one that is commonly passed with the playbook command is:

- __cluster_state__: this sets the program flow of the playbook, and has the
following options:
  - __present__: create the infrastructure, but do not initialize the cluster
  - __running__: ensure the infrastructure is started and initialize the cluster
  - __absent__: destroy the infrastructure

#### Main Playbook File

The main playbook file is site.yml. This file will orchestrate which roles to
run based on the __cluster_state__ variable.

```bash
ansible-playbook site.yml -e "cluster_state=running"
```

This will ensure that the infra exists, is running, and the cluster is
initialized. Right now the playbook assumes a single master configuration
with an arbitrary number of worker nodes. Once the playbook finishes, you
can ssh into the master and run the following command to verify the install.

```bash
kubectl get all --namespace=kube-system
```

##### Roles

The following roles are included to encapsulate the tasks

- __k8s_infrastructure_bootstrap__: create ec2 instances
- __k8s_dependencies_bootstrap__: install k8s dependencies on all machines
- __k8s_masters_bootstrap__: initialize the cluster using kubeadm
- __k8s_workers_bootstrap__: join the worker nodes to the cluster

### Credits

This playbook is based on the one that is provided in this  
[Digital Ocean Tutorial](https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04).
