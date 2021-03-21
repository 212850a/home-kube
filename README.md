# home-kube
Ansible playbook to deploy the following k3s components which are described on [my blog](http://blog.sozinov.eu):
* MetalLB
* NFS-Client
* Pihole
* Prometheus & Grafana (as stack)

## Before to run hosts.ini should be defined as example:
```
[master]
192.168.1.21

[additional_master]
192.168.1.22
192.168.1.23

[node]
192.168.1.24

[k3s_cluster:children]
master
additional_master
node

[k3s_cluster:vars]
ansible_ssh_user=vagrant
metallb_ip_range="192.168.1.2-192.168.1.10"
pihole_ip="192.168.8.11"
nfs_server_ip="192.168.8.20"
nfs_server_path="/mnt/kubcluster"
snmp_target_ip="192.168.8.1"
```
## Example of usage
```
ansible-playbook -i hosts.ini home-kube.yml
```
## Remove all roles
```
ansible-playbook -i hosts.ini reset.yml
```
