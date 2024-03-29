# home-kube
Ansible playbook to deploy the following k3s components which are described in the [blog](http://blog.sozinov.eu):
* MetalLB
* NFS-Client
* Pihole
* Prometheus & Grafana (as stack)
* Plex
* Home Assistant
* Minio
* Vaultwarden

MetalLB is used as main LoadBalancer for all services. Ip addresses will be used from default MetalLB pool, for Pihole, Plex and Minio ip-addresses have to be reserved in advanced, for others ip-addresses may not be specified - in that case they will be used from default pool. IP-addresses which are specified (reserved) for services have to be out of default pool.

For Plex nfs_server_ip:plex_nfs_data_path will be added to /etc/fstab on all nodes and mounted locally to plex_local_data_path.
It is not recommended to use RPi for Plex - practice shows that even RPi 4 does not cope with Plex load.
In current version of repo all plex related variables and ansible commands are commented out.

For Vaultwarden private key (vw.key) and certificate (vw.crt) files should be manually copied into /config of vaultwarden persistent volume after container is created, then container has to be restarted otherwise https interface won't work.

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
ansible_ssh_user=pi
metallb_ip_range="192.168.8.2-192.168.8.10"
nfs_server_ip="192.168.8.19"
nfs_server_path="/mnt/kubcluster"
snmp_targets={'router1': '192.168.8.1', 'router2': '192.168.8.2'}
icmp_targets={'router1': '192.168.8.1', 'router2': '192.168.8.2'}
http_targets={'google.com': 'https://www.google.com', 'amazon.de': 'https://www.amazon.de'}
prometheus_ip="192.168.8.20"
# plex_nfs_data_path="/volume/data"
# plex_local_data_path="/mnt/data"
# plex_claim_token="claim-ne3xhadLMsxRpy4567890"
# plex_timezone="Europe/Vilnius"
# plex_allowed_networks="192.168.8.0/24"
minio_access="minio"
minio_secret="supersecret"
# ip-addresses reserved for loadbalancer services (should not clash with metallb_ip_range)
pihole_ip="192.168.8.11"
# prometheus_ip="192.168.8.20"
# grafana_ip="192.168.8.21"
# plex_ip="192.168.8.22"
# ha_ip="192.168.8.23"
# esphome_ip="192.168.8.24"
minio_ip="192.168.8.25"
vaultwarden_ip="192.168.8.26"
# if helm chart version is not set the latest will be used
helm_chart_version_metallb="0.13.9"
helm_chart_version_nfs_subdir_external_provisioner="4.0.14"
helm_chart_version_pihole="1.9.1"
helm_chart_version_kube_prometheus_stack="16.7.0"
helm_chart_version_prometheus_snmp_exporter="1.1.0"
helm_chart_version_prometheus_blackbox_exporter="8.2.0"
helm_chart_version_home_assistant="13.4.2"
helm_chart_version_esphome="8.4.2"
helm_chart_version_minio="8.0.10"
helm_chart_version_vaultwarden="5.0.0"

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
Tags for reset playbook can be used either - to remove specific components only.
