# kubernetes-cluster-demo

## Managed Kubernetes vs self-managed Kubernetes

### Overview 🔧

In order to demonstrate the advantages and disadvantages of managed kubernetes and self managed kubernetes, I will create a self managed kubernetes cluster with Kops.

### What does it mean to have "managed Kubernetes"?

Despite the fact that Kubernetes is free source, many firms who want to use it don't have the skills or resources to set up and manage the cluster. Managed Kubernetes providers assist those interested in using Kubernetes by providing the necessary support and management for Kubernetes clusters. A managed Kubernetes deployment should offer users a painless control plane, simple deployment options, and ongoing Kubernetes maintenance, allowing them to focus on their company and getting their apps to market.

Many cloud providers offer managed Kubernetes services, however the configuration options and degree of support they provide to their customers vary slightly.

### Self Managed Cluster
There are numerous advantages and disadvantages of utilizing a self-managed Kubernetes cluster.

You have control over the management layer on a self-managed Kubernetes Cluster as a pro. Because the cluster master is handled by the managed service, fully managed Kubernetes services in the cloud do not allow you to configure it. You get complete access to the cluster master and all other related management components when you deploy your own cluster using kubeadm, kubespray, Kops or even the hard way.

This also gives configuration flexibility, allowing you to setup the cluster and nodes however you wish, rather than having to deal with the configuration options allowed by a managed service.


A managed Kubernetes cluster is preferable to a self-managed one if and only if the following conditions are met:

You wish to remain on a single cloud.
You do not require the most recent Kubernetes releases.
You're ready to delegate vulnerability patching to your cloud provider.


### Kubernetes tools to Create and Control Clusters
among the tools used to spawn and manage a kubernetes clusters, hereby I have listed some of them:
- Kubeadm – a toolkit providing the best-practice ways to create Kubernetes clusters. It is aimed at ensuring a quick and easy set up of the minimum viable cluster.
- Kubespray – a composition of Ansible tools for performing configuration and deployment-related tasks. It is compatible with any cloud service provider and is backed by Kubeadm.
- Kubewatch – a native watcher running in the cluster that monitors any changes in the system and publishes notifications in communication channels.
- Kubetail – a bash script allowing to “tail” logs into a single stream from many pods.
- Istio – an open-source platform ensuring secure and manageable traffic flow, microservices integration, as well as telemetry data integration.
- HELM – a tool that allows users to manage the so-called Helm charts (Kubernetes packaged resources). You can find popular charts or create new ones for your application.
- Kops – a tool for creating/deleting, controlling, and upgrading production-based clusters from the command line. It is used to manage the Kubernetes cluster’s entire life cycle.
- Kubernetes Dashboard – a web-based user interface that allows users to control and troubleshoot the apps in the cluster, as well as monitor the whole cluster.
- Prometheus – a monitoring & analyzing system which is extremely useful and informative while being simple to integrate and use.


### Infrastructure details

![cluster diagram](images/lb_ingress.png) [1]

### Prerequisites 📋
- Kops
- Ansible
- AWS account

### Deployment 
- Clone kubespray open source project
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```
- change infrastructure details for terraform scripts.
```
cat contrib/terraform/aws/terraform.tfvars

#Bastion Host
aws_bastion_num  = 1
aws_bastion_size = "t2.micro"

#Kubernetes Cluster
aws_kube_master_num       = 2
aws_kube_master_size      = "t2.small"
aws_kube_master_disk_size = 50

aws_etcd_num       = 1
aws_etcd_size      = "t2.micro"
aws_etcd_disk_size = 50

aws_kube_worker_num       = 2
aws_kube_worker_size      = "t2.small"
aws_kube_worker_disk_size = 50

```

- Execute ansible on main kubespray directory
```
cd kops

ansible-playbook create-store

ansible-playbook create.yml

#ansible-playbook -i inventory --become cluster.yml -e cloud_provider=aws -e ansible_user=INSTANCE_USER -e cloud_provider=aws -e kube_network_plugin=flannel -b --become-user=root --flush-cache -e ansible_ssh_private_key_file=SSH_PRIVATE_FILE
```

- ingress controller after installing the cluster with kubespray and terraform
```
kubectl --kubeconfig=config_master apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/cloud/deploy.yaml
```

### Comparisson 
- The provider-managed Kubernetes service reduces the time and effort required to administer and maintain a cluster by taking care of the master node.

- You have more control over your cluster using self-managed Kubernetes. You can use various cloud computing services and even you can use your own on-site infrastructure.

### Testing
![Cluster details summary](images/cluster_nginx_lb.png)

- Testing with aws-controller lb 

### References
- https://opsani.com/blog/kubernetes-cluster-autoscaling-overview/ 
- https://www.digitalocean.com/blog/vanilla-kubernetes-vs-managed-kubernetes/#:~:text=Although%20Kubernetes%20is%20open%20source,maintenance%20of%20the%20Kubernetes%20clusters. 
- [1] https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/

 
