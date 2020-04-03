![](https://img.shields.io/badge/Ansible-kubernetes-green.svg?logo=angular&style=for-the-badge)

>__Please note that the original design goal of this role was more concerned with the initial installation and bootstrapping environment, which currently does not involve performing continuous maintenance, and therefore are only suitable for testing and development purposes,  should not be used in production environments.__

>__请注意，此角色的最初设计目标更关注初始安装和引导环境，目前不涉及执行连续维护，因此仅适用于测试和开发目的，不应在生产环境中使用。__
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
- [Contributors](#Contributors)

## Overview
This Ansible role installs Kubernetes on linux operating system, including establishing a filesystem structure and server configuration with some common operational features, very easy to deploy if you use HashiCorp Consul as DNS-based service discovery.

## Requirements
### Operating systems
This role will work on the following operating systems:

  * CentOS 7

### Kubernetes versions

The following list of supported the kubernetes releases:

- Core
  - [kubernetes](https://github.com/kubernetes/kubernetes) v1.14
  - [kubernetes](https://github.com/kubernetes/kubernetes) v1.15
  - [kubernetes](https://github.com/kubernetes/kubernetes) v1.16
  - [kubernetes](https://github.com/kubernetes/kubernetes) v1.17
- Network Plugin
  - [calico](https://github.com/projectcalico/calico) v3.13.1
  - [flannel](https://github.com/coreos/flannel) v0.12.0
  - [canal](https://github.com/projectcalico/canal) (given calico/flannel versions)
- Components
  - [kubernetes-dashboard](https://github.com/kubernetes/dashboard) v2.0.0-rc7
  - [metrics-server](https://github.com/kubernetes-sigs/metrics-server) v0.3.6
  - [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) v1.9.5

## Role variables
### Main parameters #
There are some variables in defaults/main.yml which can (Or needs to) be overridden:

##### General parameters
* `kube_version`: Specify the Kubernetes version.
* `kube_node_role`: Type of nodes in cluster, master or node.
* `kube_cgroup_driver`: Specifies the management of the container's cgroups, cgroupfs or systemd.
* `kube_control_plane_endpoint`: The address or DNS and port of the load balancer.

##### Kubernetes networking
* `kube_network.cni`: Specify the Kubernetes CNI plugins, calico / flannel / canal.
* `kube_network.pod_cidr`: Specify range of IP addresses for the pod network.
* `kube_network.srv_cidr`: Use alternative range of IP address for service VIPs.

##### Dashboard parameters
* `kube_dashboard_install`: A boolean value, whether installs kubernetes dashboard.

##### Components
* `kube_components`: Individual Kubernetes components.

##### Role dependencies
* `kube_docker_dept`: A boolean value, whether Docker installs use the same task.

##### Docker parameters
* `kube_docker_version`: Specify the Docker version.
* `kube_docker_edition`: Specify the Docker edition.
* `kube_docker_channel`: Define Docker distribution.
* `kube_docker_syslog`: A boolean value,  Enable or Disable send log to remote Syslog server.

##### Service Mesh
* `environments`: Define the service environment.
* `tags`: Define the service custom label.
* `consul_public_exporter_token`: Public Consul client ACL token.
* `consul_public_http_prot`: The consul Hypertext Transfer Protocol.
* `consul_public_clients`: List of public consul clients.
* `consul_public_http_port`: The consul HTTP API port.

### Other parameters
There are some variables in vars/main.yml:

## Dependencies
- Ansible versions >= 2.8 are supported.
- [Docker](https://github.com/goldstrike77/ansible-role-linux-docker.git)

## Example

### Hosts inventory file
See tests/inventory for an example.
    
    [Node]
    s-p-demo-nod01 ansible_host='10.101.4.40'
    s-p-demo-nod02 ansible_host='10.101.4.41'
    s-p-demo-nod03 ansible_host='10.101.4.42'
    
    [Master]
    s-p-demo-mas01 ansible_host='10.101.4.43'
    s-p-demo-mas02 ansible_host='10.101.4.44'
    s-p-demo-mas03 ansible_host='10.101.4.45'
    
    [Kubernetes:vars]
    kube_version='1.15.11'
    kube_control_plane_endpoint='Master-Production-APIServer.service.dc01.local:6443'
    kube_docker_dept=true
    kube_docker_version='18.09.9'
    
    [Master:vars]
    kube_node_role='master'
    kube_network.cni='canal'
    kube_network.pod_cidr='10.244.0.0/16'
    kube_network.srv_cidr='10.96.0.0/12'
    
    [Kubernetes:children]
    Master
    Node

### Combination of group vars and playbook
You can also use the group_vars or the host_vars files for setting the variables needed for this role. File you should change: group_vars/all or host_vars/`group_name`

    kube_version: '1.15.11'
    kube_node_role: 'node'
    kube_cgroup_driver: 'systemd'
    kube_control_plane_endpoint: 'Master-Production-APIServer.service.dc01.local:6443'
    kube_network:
      cni: 'canal'
      pod_cidr: '10.244.0.0/16'
      srv_cidr: '10.96.0.0/12'
    kube_dashboard_install: true
    kube_components:
      - 'metrics-server'
      - 'kube-state-metrics'
    kube_docker_dept: true
    kube_docker_version: '18.09.9'
    kube_docker_edition: 'ce'
    kube_docker_channel: 'stable'
    kube_docker_syslog: false
    kube_port_arg:
      etcd: '2379-2380'
      cadvisor: '4149'
      api_server: '6443'
      dashboard: '8001'
      calico: '9099'
      kube: '10250-10256'
      node: '30000-32767'
    environments: 'Development'
    tags:
      subscription: 'default'
      owner: 'nobody'
      department: 'Infrastructure'
      organization: 'The Company'
      region: 'IDC01'
    consul_public_exporter_token: '00000000-0000-0000-0000-000000000000'
    consul_public_http_prot: 'https'
    consul_public_http_port: '8500'
    consul_public_clients:
      - '127.0.0.1'

## License
![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Contributors
Special thanks to the [Connext Information Technology](http://www.connext.com.cn) for their contributions to this role.
