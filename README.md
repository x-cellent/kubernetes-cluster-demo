# kubernetes-cluster-demo

## Managed Kubernetes vs self-managed Kubernetes

### Overview 🔧

Kubernetes clusters can be controlled by the user (self-managed) or by a cloud service provider (Provider managed Kubernetes). Unless you have a compelling technical or legal reason to use an on-premises self-managed cluster, consider a managed Kubernetes service. Self-managed clusters necessitate a high level of specialized expertise among your staff, as well as continuous infrastructure and maintenance costs.

In order to demonstrate the advantages and disadvantages of managed kubernetes and self managed kubernetes, I will create a self managed kubernetes cluster with Kops.

### What does it mean to have "managed Kubernetes"? 

Despite the fact that Kubernetes is free source, many firms who want to use it don't have the skills or resources to set up and manage the cluster. Managed Kubernetes providers assist those interested in using Kubernetes by providing the necessary support and management for Kubernetes clusters. A managed Kubernetes deployment should offer users a painless control plane, simple deployment options, and ongoing Kubernetes maintenance, allowing them to focus on their company and getting their apps to market.

Many cloud providers offer managed Kubernetes services, however the configuration options and degree of support they provide to their customers vary slightly.

A managed Kubernetes cluster is preferable to a self-managed one if we take the following into consideration:

- A single cloud
- You do not require the most recent Kubernetes releases.
- You're ready to delegate vulnerability patching to your cloud provider.


### Self Managed Clusters 📋
There are numerous advantages and disadvantages of utilizing a self-managed Kubernetes cluster.

As a matter of fact, you have control over the management layer on a self-managed Kubernetes Cluster, whereas the master is handled by the provider in a fully managed kubernetes service not allowing you to configure it at all. Moreover, you get complete access to the cluster master and all other related management components when you deploy your own cluster using kubeadm, kubespray, Kops or even the hard way.

This also gives configuration flexibility, allowing you to setup the cluster and nodes however you wish, rather than having to deal with the configuration options allowed by a managed service.

### Kubernetes tools to Create and Control Clusters 📖
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

### kops stands for Kubernetes Operations.
"We like to think of it as kubectl for clusters," says the kops GitHub website. kops is a command line interface (CLI) tool that has implemented the major verbs of cluster management. Among the traits that make kops appealing are:

Provisioning that would otherwise have to be done the hard way, which is now more automated.
It features configuration parameters that are externalized and modifiable.
Updates to Kubernetes configuration parameters are used.
Deploying Kubernetes primary nodes in high-availability (HA) mode is made easier.
Upgrades to Kubernetes versions are supported.
For dry-runs, a state-sync model was used, as well as automated idempotency.
Terraform configuration generation capability
Custom kubectl add-ons are supported.

### Infrastructure details

![cluster diagram](images/lb_ingress.png) [1]

### Prerequisites 📋
- Kops
- Ansible
- AWS account
- docker
- A dns domain

### Deployment 
- Clone kubespray open source project
```
git clone https://github.com/x-cellent/kubernetes-cluster-demo

```
- Create a domain for your cluster, otherwise you can use a gossip based domain

- change infrastructure details for kops scripts.
```
cat group_vars/all.yml
cluster_name: kops-xc.xc-cloud.net #kub-xcellent.xc-cloud.net
#cluster_name: kubernetes.xc-cloud.net
state_store: s3://kops-cluster-xc-test

## Remote access
ssh_public_key: ~/.ssh/id_rsa.pub


## Regions / Availability zones
aws_region: eu-central-1
aws_zones: eu-central-1a,eu-central-1b,eu-central-1c
# Use master_zones if you want your masters in different zones than worker nodes.
# Useful especially when you want 1 master in single zone but workers in several zones.
# When not defined, aws_zones will be sued instead
master_zones: eu-central-1a,eu-central-1b,eu-central-1c
#master_zones: eu-central-1a


## Network
dns_zone: kops-xc.xc-cloud.net
network_cidr: 172.16.0.0/16
#kubernetes_networking: amazonvpc
kubernetes_networking: amazon-vpc-routed-eni 
#kubernetes_networking: weave
# Topology must be either public or private
topology: private
bastion: true

# Master configuration
master_size: t2.small
master_count: 3
#master_volume_size: 50
# Use master_max_price if you want to use spot instances.
# Useful to reduce cost during testing
#master_max_price: 0.05
# Use master_profile if you want custom iam policies
#master_profile: arn:aws:iam::1234567890108:instance-profile/kops-custom-master-role

# Node configuration
node_size: t2.small
node_count: 3

```

- Create S3 bucket to store kops cluster state
```

ansible-playbook create-store

```

Create cluster 
```

ansible-playbook create.yml

```

- ingress controller after installing the cluster with kubespray and terraform
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/cloud/deploy.yaml
```

### Comparisson 
- The provider-managed Kubernetes service reduces the time and effort required to administer and maintain a cluster by taking care of the master node.

- You have more control over your cluster using self-managed Kubernetes. You can use various cloud computing services and even you can use your own on-site infrastructure.


### Testing
![Cluster details summary](images/cluster_nginx_lb.png)

### Conclusion
In the end, either approach — managed or unmanaged Kubernetes — will yield a functional Kubernetes environment. Whether one strategy is better for your team than another is determined by your level of Kubernetes expertise, how quickly you want to get your clusters up and running, and how important it is for you to have complete control over how your clusters are designed and configured.


### References
- https://opsani.com/blog/kubernetes-cluster-autoscaling-overview/ 
- https://www.digitalocean.com/blog/vanilla-kubernetes-vs-managed-kubernetes/#:~:text=Although%20Kubernetes%20is%20open%20source,maintenance%20of%20the%20Kubernetes%20clusters. 
- [1] https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/
- https://dzone.com/articles/which-managed-kubernetes-is-right-for-me
- https://containerjournal.com/features/choosing-a-managed-kubernetes-provider/
- https://www.eficode.com/blog/start-well-with-kubernetes

 
