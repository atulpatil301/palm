# AWS Kubernetes

AWS Kubernetes is a Kubernetes cluster deployed using [Kubeadm](https://kubernetes.io/docs/admin/kubeadm/) tool. It provides full integration with AWS. It is able to handle ELB load balancers, EBS disks, Route53 domains and other AWS resources.


## Prerequisites and dependencies
AWS Kubernetes deployes into an existing VPC / public subnet. If you don't have your VPC / subnet yet, you can use [this](https://github.com/scholzj/aws-vpc) configuration to create one. To deploy AWS Kubernetes there are no other dependencies apart from [Terraform](https://www.terraform.io). Kubeadm is used only on the EC2 hosts and doesn't have to be installed locally.

## Configuration

The configuration is done through Terraform variables. Example *tfvars* file is part of this repo and is named `example.tfvars`. Change the variables to match your environment / requirements before running `terraform apply ...`.

| Option | Explanation | Example |
|--------|-------------|---------|
| `aws_region` | AWS region which should be used | `eu-central-1` |
| `cluster_name` | Name of the Kubernetes cluster (also used to name different AWS resources) | `my-aws-kubernetes` |
| `master_instance_type` | AWS EC2 instance type for master | `t2.medium` |
| `worker_instance_type` | AWS EC2 instance type for worker | `t2.medium` |
| `ssh_public_key` | SSH key to connect to the remote machine | `~/.ssh/id_rsa.pub` |
| `master_subnet_id` | Subnet ID where master should run | `subnet-8d3407e5` |
| `worker_subnet_ids` | List of subnet IDs where workers should run | `[ "subnet-8d3407e5" ]` |
| `min_worker_count` | Minimal number of worker nodes | `3` |
| `max_worker_count` | Maximal number of worker nodes | `6` |
| `hosted_zone` | DNS zone which should be used | `my-domain.com` |
| `hosted_zone_private` | Is the DNS zone public or private | `false` |
| `addons` | List of addons which should be installed | `[ "https://..." ]` |
| `tags` | Tags which should be applied to all resources | see *example.tfvars* file |
| `tags2` | Tags in second format which should be applied to AS groups | see *example.tfvars* file |
| `ssh_access_cidr` | List of CIDRs from which SSH access is allowed | `[ "0.0.0.0/0" ]` |
| `api_access_cidr` | List of CIDRs from which API access is allowed | `[ "0.0.0.0/0" ]` |

### Using multiple / different subnets for workers nodea

In order to run workers in additional / different subnet(s) than master you have to tag the subnets with `kubernetes.io/cluster/{cluster_name}=shared`. For example `kubernetes.io/cluster/my-aws-kubernetes=shared`. During the cluster setup, the bootstrapping script will automatically add these tags to the subnets specified in `worker_subnet_ids`.

## Creating AWS Kubernetes Cluster

To create AWS Kubernetes cluster, 
* Export AWS credentials into environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
* Apply Terraform configuration:
```bash
terraform apply --var-file example.tfvars
```

## Deleting AWS Kubernetes Cluster

To delete AWS Kubernetes cluster, 
* Export AWS credentials into environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
* Destroy Terraform configuration:
```bash
terraform destroy --var-file example.tfvars
```

## Addons

Currently, following addons are supported:
* Kubernetes dashboard
* Heapster for resource monitoring
* Storage class for automatic provisioning of persisitent volumes
* External DNS (Replaces Route53 mapper)
* Ingress
* Autoscaler
* Logging with Fluentd + ElasticSearch + Kibana

The addons will be installed automatically based on the Terraform variables. 

## Custom Addons

Custom addons can be added if needed. For every URL in the `addons` list, the initialization scripts will automatically call `kubectl -f apply <Addon URL>` to deploy it. The cluster is using RBAC. So the custom addons have to be *RBAC ready*.

## Tagging

If you need to tag resources created by your Kubernetes cluster (EBS volumes, ELB load balancers etc.) check [this AWS Lambda function which can do the tagging](https://github.com/scholzj/aws-kubernetes-tagging-lambda).


### How to access the Kubernetes Dashboard

The Kubernetes Dashboard addon is by default not exposed to the internet. This is intentional for security reasons (no authentication / authorization) and to save costs for Amazon AWS ELB load balancer.

You can access the dashboard easily fro any computer with installed and configured `kubectl`:
1) From coomand line start `kubectl proxy`
2) Go to your browser and open [http://127.0.0.1:8001/ui](http://127.0.0.1:8001/ui)


## Start Deployment Web Application

Note: I have already created Images which located on AWS ECR.

Lets Start deploy application.


## create namespace.

   kubectl create ns name of namespace
   
## Deploy Application

kubectl create -f palm/pam.yml

