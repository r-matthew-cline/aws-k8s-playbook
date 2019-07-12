# AWS Kubernetes Bootstrap

This repo contains an Ansible playbook that can be executed from a remote host
to bootstrap a single master Kubernetes cluster with an arbitrary number of
worker nodes in AWS. 

### Dependencies

- Python on all hosts
- Ansible
- AWS VPC configured with NACL, Security Groups, and Subnets in place

### Running The Playbook

#### Hosts File

The hosts file is where you will configure the information needed to connect
to the Kubernetes hosts. The "masters" group should contain you k8s master
node. The "workers" group should contain all of your worker nodes. The
"all:vars" block should contain the path to the python executable on all
of the remote hosts. The variables for the nodes are listed below:

- __name:__ the name of the node. Only used for ansible.
- __ansible_host:__ the ip address of the node for ssh.
- __ansible_user:__ the user with sudo priviledges on the remote nodes.

#### User Creation File

You will need to update the SSH key file location in the user_creation.yml
file. This key will be used for the k8s user that is created on all of the
nodes.

#### Main Playbook File

The k8s_bootstrap.yml file is the orchestrator for the playbook. The 
other yaml files contain the actual tasks that need to be executed.
When running the playbook you will need to pass in the hosts file using the 
command below. You will also need to pass the --ask-become-pass flag, 
so that you can pass the sudo password for all of the other machines. If the
other nodes have different passwords or use ssh keys, you will have to modify
the playbook. That functionality is not currently included.

```bash
ansible-playbook -i hosts k8s_bootstrap.yml --ask-become-pass
```

This will prompt you for the sudo password of the users on the remote systems.
Once you enter the password, the playbook will run and configure the cluster.
After the playbook runs, you can log into the master using the k8s account 
that is created using the ssh key provided. From there you can verify the 
cluster is running using the following command.

```bash
kubectl get all --namespace=kube-system
```

### Credits

This playbook is based on the one that is provided in this  
[Digital Ocean Tutorial](https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04).
