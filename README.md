# home-kube
Ansible playbook to deploy the following k3s components which are described in the [blog](http://blog.sozinov.eu):
* MetalLB
* NFS-Client
* Pihole
* Prometheus & Grafana (as stack)
* Plex

MetalLB is used as main LoadBalancer for all services. For Pihole ip-address should be specified, for all other services (Prometheus, Grafana and Plex) ip-addresses will be taken from defined default MetalLB pool.

For Plex nfs_server_ip:plex_nfs_data_path will be added to /etc/fstab on all nodes and mounted locally to plex_local_data_path.

Helm should be installed on first defined master manually as per [official guide](https://helm.sh/docs/intro/install/) before to proceed further.

## Before to run hosts.ini should be defined as example:
```
[master]
192.168.1.21
192.168.1.22
192.168.1.23

[node]
192.168.1.24

[k3s_cluster:children]
master
node

[k3s_cluster:vars]
ansible_ssh_user=vagrant
metallb_ip_range="192.168.1.2-192.168.1.10"
pihole_ip="192.168.8.11"
nfs_server_ip="192.168.8.20"
nfs_server_path="/mnt/kubcluster"
snmp_target_ip="192.168.8.1"
plex_nfs_data_path="/volume/data"
plex_local_data_path="/mnt/data"
plex_claim_token="claim-ne3xhadLMsxRpy4567890"
plex_timezone="Europe/Vilnius"
```
All Kubernetes actions are performed to first defined master, so please ensure it's accessible and working fine.
## Example of usage
```
ansible-playbook -i hosts.ini home-kube.yml
```
## Usage of tags
Each role can be executed separately with a help of tags, like:
```
ansible-playbook -i hosts.ini --tags pihole home-kube.yml
```

## Remove all roles
```
ansible-playbook -i hosts.ini reset.yml
```
