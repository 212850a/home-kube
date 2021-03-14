# home-kube
Before to run hosts.ini should be defined as example:
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
```
Example of usage
```
ansible-playbook vagrant-kube.yml
```
