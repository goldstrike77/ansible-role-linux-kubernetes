![](https://img.shields.io/badge/Ansible-kubernetes-green.svg?logo=angular&style=for-the-badge)

>__Please note that the original design goal of this role was more concerned with the initial installation and bootstrapping environment, which currently does not involve performing continuous maintenance, and therefore are only suitable for testing and development purposes,  should not be used in production environments. The author does not guarantee the accuracy, completeness, reliability, and availability of the role content. Under no circumstances will the author be held responsible or liable in any way for any claims, damages, losses, expenses, costs or liabilities whatsoever, including, without limitation, any direct or indirect damages for loss of profits, business interruption or loss of information.__

>__请注意，此角色的最初设计目标更关注初始安装和引导环境，目前不涉及执行连续维护，因此仅适用于测试和开发目的，不应在生产环境中使用。作者不对角色内容之准确性、完整性、可靠性、可用性做保证。在任何情况下，作者均不对任何索赔，损害，损失，费用，成本或负债承担任何责任，包括但不限于因利润损失，业务中断或信息丢失而造成的任何直接或间接损害。__
___

<p><img src="https://raw.githubusercontent.com/goldstrike77/goldstrike77.github.io/master/img/logo/logo_kubernetes.png" align="right" /></p>

__Table of Contents__

- [Overview](#overview)
- [Requirements](#requirements)
  * [Operating systems](#operating-systems)
  * [Kubernetes Versions](#kubernetes-versions)
- [ Role variables](#Role-variables)
  * [Main Configuration](#Main-parameters)
  * [Other Configuration](#Other-parameters)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
  * [Hosts inventory file](#Hosts-inventory-file)
  * [Vars in role configuration](#vars-in-role-configuration)
  * [Combination of group vars and playbook](#combination-of-group-vars-and-playbook)
- [License](#license)
- [Author Information](#author-information)
- [Donations](#Donations)

## Overview
Kubernetes is an open-source container-orchestration system for automating application deployment, scaling, and management. It was originally designed by Google, and is now maintained by the Cloud Native Computing Foundation. This Ansible role installs Kubernetes on linux operating system, including establishing a filesystem structure and cluster configuration with some common operational features, very easy to deploy if you use HashiCorp Consul as DNS-based service discovery.
>__There are two file that record token for prometheus and dashboard in /tmp folder at first master node, Burn after reading!__

## Requirements
### Operating systems
This role will work on the following operating systems:

  * CentOS 7
  * Rocky 8

### Kubernetes versions

The following list of supported the kubernetes releases:

- Core
  - [kubernetes](https://github.com/kubernetes/kubernetes) v1.22
- Network Plugin
  - [flannel](https://github.com/coreos/flannel) v0.19.1
- Components
  - [kubernetes-dashboard](https://github.com/kubernetes/dashboard) v2.6.1
  - [k9s](https://github.com/derailed/k9s)

## Role variables
### Main parameters #
There are some variables in defaults/main.yml which can (Or needs to) be overridden:

##### General parameters
* `kube_version`: Specify the Kubernetes version.
* `kube_node_role`: Type of nodes in cluster, master or node.
* `kube_clustername`: Specify the Kubernetes cluster name.
* `kube_strictarp`: A boolean to determine whether or not to enable strict ARP.
* `kube_proxy_ipvs`: A boolean to determine whether or not to run kube-proxy in IPVS mode.
* `kube_control_plane_endpoint`: The address or DNS name of the API Server load balancer advertise listening on.
* `kube_control_plane_port`: The port of the API Server load balancer advertise listening on.
* `kube_node_extra_labels`: Defined node labels.

##### Kubernetes networking
* `kube_cni`: Specify the Kubernetes CNI plugins.
* `kube_pod_cidr`: Specify range of IP addresses for the pod network.
* `kube_srv_cidr`: Use alternative range of IP address for service VIPs.

##### Dashboard parameters
* `kube_dashboard_install`: A boolean to determine whether or not to deploy kubernetes dashboard.

##### Backup parameters
* `kube_backupset_arg.keep`: Backup retention cycle in days.
* `kube_backupset_arg.cloud_rsync`: Whether rsync for cloud storage.
* `kube_backupset_arg.cloud_drive`: Specify the cloud storage providers, azureblob / s3 for minio.
* `kube_backupset_arg.cloud_bwlimit`: Controls the bandwidth limit.
* `kube_backupset_arg.cloud_event`: Define transfer events.
* `kube_backupset_arg.cloud_config`: Specify the cloud storage configuration.

##### Role dependencies
* `kube_docker_dept`: A boolean to determine whether or not to install Docker at the same task.

##### Docker parameters
* `kube_docker_version`: Specify the Docker version.
* `kube_docker_edition`: Specify the Docker edition.
* `kube_docker_channel`: Define Docker distribution.
* `kube_docker_path`: Specify the Docker data folder.
* `kube_docker_bip`: Specify the Docker network bridge IP.
* `kube_docker_syslog`: A boolean value, Enable or Disable send log to remote Syslog server.

##### K9s parameters
* `kube_k9s_version`: Specify the K9s version.

##### Service Mesh
* `environments`: Define the service environment.
* `datacenter`: Define the DataCenter.
* `domain`: Define the Domain.
* `customer`: Define the customer name.
* `tags`: Define the service custom label.
* `exporter_is_install`: Whether to install prometheus exporter.
* `consul_public_register`: Whether register a exporter service with public consul client.
* `consul_public_exporter_token`: Public Consul client ACL token.
* `consul_public_http_prot`: The consul Hypertext Transfer Protocol.
* `consul_public_clients`: List of public consul clients.
* `consul_public_http_port`: The consul HTTP API port.

### Other parameters
There are some variables in vars/main.yml:

## Dependencies
- Ansible versions >= 2.8
- Python >= 2.7.5
- [Docker](https://github.com/goldstrike77/ansible-role-linux-docker.git)

## Example

### Hosts inventory file
See tests/inventory for an example.
    
    [Operator]
    dem-p-inf-mas01 ansible_host='10.101.4.43' kube_node_role='master' kube_node_extra_labels='["nodeType=master", "application=Platform"]'
    dem-p-inf-mas02 ansible_host='10.101.4.44' kube_node_role='master' kube_node_extra_labels='["nodeType=master", "application=Platform"]'
    dem-p-inf-mas03 ansible_host='10.101.4.45' kube_node_role='master' kube_node_extra_labels='["nodeType=master", "application=Platform"]'
    dem-p-inf-nod01 ansible_host='10.101.4.40' kube_node_extra_labels='["kubernetes.azure.com/cluster=true", "longhorn/node=true", "application=Platform", "nodeType=slave"]'
    dem-p-inf-nod02 ansible_host='10.101.4.41' kube_node_extra_labels='["kubernetes.azure.com/cluster=true", "longhorn/node=true", "application=Platform", "nodeType=slave"]'
    dem-p-inf-nod03 ansible_host='10.101.4.42' kube_node_extra_labels='["kubernetes.azure.com/cluster=true", "longhorn/node=true", "application=Platform", "nodeType=slave"]'
    
    [Operator:vars]
    kube_version='1.22.12'
    kube_control_plane_endpoint='demo-prd-infra-k8s00-apiserver.service.dc01.local'

### Combination of group vars and playbook
You can also use the group_vars or the host_vars files for setting the variables needed for this role. File you should change: group_vars/all or host_vars/`group_name`.

```yaml
kube_version: '1.22.12'
kube_node_role: 'node'
kube_clustername: 'kubernetes'
kube_strictarp: true
kube_proxy_ipvs: true
kube_control_plane_endpoint: 'demo-prd-infra-k8s00-apiserver.service.dc01.local'
kube_control_plane_port: '6443'
kube_cni: 'flannel'
kube_pod_cidr: '10.244.0.0/16'
kube_srv_cidr: '10.96.0.0/12'
kube_dashboard_install: true
kube_backupset_arg:
  keep: '5'
  cloud_rsync: true
  cloud_drive: 'azureblob'
  cloud_bwlimit: '10M'
  cloud_event: 'sync'
  cloud_config:
    account: 'blobuser'
    key: 'base64encodedkey=='
    endpoint: 'blob.core.chinacloudapi.cn'
kube_docker_dept: true
kube_docker_version: '19.03.15'
kube_port_tcp_arg:
  etcd: '2379-2380'
  api: '6443'
  kube: '10250-10256'
  node: '30000-32767'
kube_cni_tcp_arg:
  calico_bgp: '179'
  calico_typha: '5473'
kube_cni_udp_arg:
  flannel_vxlan: '8472'
  flannel_debug: '8285'
kube_k9s_version: '0.25.21'
environments: 'prd'
datacenter: 'dc01'
domain: 'local'
customer: 'demo'
tags:
  subscription: 'default'
  owner: 'nobody'
  department: 'Infrastructure'
  organization: 'The Company'
  region: 'China'
exporter_is_install: true
consul_public_register: true
consul_public_exporter_token: '00000000-0000-0000-0000-000000000000'
consul_public_http_prot: 'https'
consul_public_http_port: '8500'
consul_public_clients:
  - '127.0.0.1'
```

## License
![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Donations
Please donate to the following monero address.

    46CHVMbb6wQV2PJYEbahb353SYGqXhcdFQVEWdCnHb6JaR5125h3kNQ6bcqLye5G7UF7qz6xL9qHLDSAY3baagfmLZABz75
<p><img src="https://raw.githubusercontent.com/goldstrike77/goldstrike77.github.io/master/img/xmr_address.png" align="left" /></p>