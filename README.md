# AWS Kubernetes

AWS Kubernetes is a Kubernetes cluster deployed using [Kubeadm](https://kubernetes.io/docs/admin/kubeadm/) tool. It provides full integration with AWS. It is able to handle ELB load balancers, EBS disks, Route53 domains and other AWS resources.


## Prerequisites and dependencies
AWS Kubernetes deployes into an existing VPC / public subnet. If you don't have your VPC / subnet yet, you can use configuration to create one. To deploy AWS Kubernetes there are no other dependencies apart from [Terraform](https://www.terraform.io). Kubeadm is used only on the EC2 hosts and doesn't have to be installed locally.

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

If you need to tag resources created by your Kubernetes cluster (EBS volumes, ELB load balancers etc.) 


### Start Deployment Web Application

Note: I have already created Images which located on AWS ECR.

##Lets Start deploy application.


## create namespace.

```bash
kubectl create ns name of namespace

```
   
## Deploy Application

```bash
kubectl create -f palm/pam.yml

```

## Describe deployments.

```bash
[centos@ip-xxxxxx ~]$ kubectl get pod -n palm
NAME                    READY     STATUS    RESTARTS   AGE
web1-794bddb69b-srg4b   1/1       Running   0          16h
web1-794bddb69b-zjh7t   1/1       Running   0          16h
web2-765cd77758-dv8tr   1/1       Running   0          16h
web2-765cd77758-trrk5   1/1       Running   0          16h
web3-5944745648-dqg4b   1/1       Running   0          16h
web3-5944745648-tn9fv   1/1       Running   0          16h

```
```bash
[centos@ip-10-79-196-128 ~]$ kubectl get svc -n palm
NAME      TYPE           CLUSTER-IP       EXTERNAL-IP                      PORT(S)          AGE
web1      LoadBalancer   172.20.xxx.xx    x1.us-east-1.elb.amazonaws.com   8080:30267/TCP   16h
web2      LoadBalancer   172.20.xxx.xx   x2.us-east-1.elb.amazonaws.com    8081:32759/TCP   16h
web3      LoadBalancer   172.20.xxx.xx    x3-east-1.elb.amazonaws.com      8082:31352/TCP   16h

```
### View the details of the deployed service.

```bash

[centos@ip-xxxxxxxxxxx ~]$ kubectl -n palm describe service web1
Name:                     web1
Namespace:                palm
Labels:                   io.dev.service=web1
Annotations:              kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Service","metadata":{"annotations":{"service.beta.kubernetes.io/azure-load-balancer-internal":"0.0.0.0/0"},"labels":{"io.dev...
                          service.beta.kubernetes.io/azure-load-balancer-internal=0.0.0.0/0
Selector:                 io.dev.service=web1
Type:                     LoadBalancer
IP:                       172.20.xxx.xx
LoadBalancer Ingress:     x1.us-east-1.elb.amazonaws.com
Port:                     8080  8080/TCP
TargetPort:               80/TCP
NodePort:                 8080  30267/TCP
Endpoints:                10.79.xx.xx:80,10.79.xxx.x:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

[centos@ip-xxxxxxxxxxx ~]$ kubectl -n palm describe service web2
Name:                     web2
Namespace:                palm
Labels:                   io.dev.service=web2
Annotations:              kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Service","metadata":{"annotations":{"service.beta.kubernetes.io/azure-load-balancer-internal":"0.0.0.0/0"},"labels":{"io.dev...
                          service.beta.kubernetes.io/azure-load-balancer-internal=0.0.0.0/0
Selector:                 io.dev.service=web2
Type:                     LoadBalancer
IP:                       172.20.xxx.xxx
LoadBalancer Ingress:     x2.us-east-1.elb.amazonaws.com
Port:                     8081  8081/TCP
TargetPort:               80/TCP
NodePort:                 8081  32759/TCP
Endpoints:                10.79.xxx.xx:80,10.79.xxx.xx:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

[centos@ip-xxxxxxxxxxx ~]$ kubectl -n palm describe service web3
Name:                     web3
Namespace:                palm
Labels:                   io.dev.service=web3
Annotations:              kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Service","metadata":{"annotations":{"service.beta.kubernetes.io/azure-load-balancer-internal":"0.0.0.0/0"},"labels":{"io.dev...
                          service.beta.kubernetes.io/azure-load-balancer-internal=0.0.0.0/0
Selector:                 io.dev.service=web3
Type:                     LoadBalancer
IP:                       172.20.xx.xxx
LoadBalancer Ingress:     x3.us-east-1.elb.amazonaws.com
Port:                     8082  8082/TCP
TargetPort:               80/TCP
NodePort:                 8082  31352/TCP
Endpoints:                10.79.xxx.xxx:80,10.79.xxx.xx:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>


```

### View the details of one of the pods that was deployed.

```bash
[centos@ip-xxxxxxxx ~]$ kubectl describe pod web1-794bddb69b-srg4b -n palm
Name:               web1-794bddb69b-srg4b
Namespace:          palm
Priority:           0
PriorityClassName:  <none>
Node:               ip-10-79-xxx.xx.ec2.internal/10.79.xxx.xx
Start Time:         Thu, 17 Sep 2020 15:00:27 +0000
Labels:             io.dev.service=web1
                    pod-template-hash=794bddb69b
Annotations:        kubernetes.io/psp=eks.privileged
Status:             Running
IP:                 10.79.xxx.x
Controlled By:      ReplicaSet/web1-794bddb69b
Containers:
  web1:
    Container ID:   docker://db88c02944ce2c3ddbc5b7233269259a46c2b0b0a7b709cdf07203da
    Image:          xxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/palm:1.0
    Image ID:       docker-pullable://xxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/palm@sha256:e0a788e004501d337ce259fc3f7d2f828f17a5ae054d927f522f4282bdaea5f2
    Ports:          80/TCP, 8080/TCP
    Host Ports:     0/TCP, 0/TCP
    State:          Running
      Started:      Thu, 17 Sep 2020 15:00:28 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-bmqzd (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-bmqzd:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-bmqzd
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>

```


