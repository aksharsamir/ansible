## Ansible installation of GTM Containers on Ubuntu LTS 22.04

Updated: 01-06-2024
Version: 1.0 
Author: P.Maas - p.maas@e-tailors.com





Setup inspired on: https://devsnit.com/en/google-tag-manager-server-side-tracking-ubuntu-22/

### Software used for setup:

* `Nginx`
* `Letsencrypt`
* `Docker`
* `Python`


### Configuration explained
On the `Ubuntu 22.04 LTS` machine we setup `Nginx` as a proxy to link the DNS entry's to the server. For SSL
we use `letsencrypt` to generate the SSL certificates based on the hostnames.

Based on type of server creation we have only the `GTM SST base container` on the server 
or both  `GTM SST base container` +  `GTM SST preview container`. This has to do with the `.env`
setting in `Laravel` to generate `1 or 2 VPS servers`. When we generate 1 VPS then we have both instances
on the same VPS. When we generate 2 seperate VPS servers, in that case we have both seperated.

We have running the GTM Docker container created by Google:
```gcr.io/cloud-tagging-10302018/gtm-cloud-image:stable```

On docker runtime we feed the `CONTAINER_CONFIG` token (known also as `gtm_config_token` )

### Configure
We have to set the correct server details in `inventory.ini` we need here the following
info:
```
[vps]
88.99.174.3                                     ==> server / VPS IP adres

[vps:vars]
ansible_ssh_user= xxxx                          ==> server / VPS root user
ansible_ssh_pass= xxxx                          ==> server / VPS root password

ansible_python_interpreter=/usr/bin/python3

[all:vars]
domain1=sst.tag-cloud.io                        ==> First hostname for SST container
domain2=debug.sst.tag-cloud.io                  ==> Second hostname for SST preview container
gtm_config_token=11111                          ==> GTM configuration token
```

### Run Ansible scripts
1. Startup ansible server from docker compose
2. Create a terminal to ssh into the ansible server 
3. RUN: `ansible-playbook -i /etc/ansible/inventory.ini  /etc/ansible/main_playbook.yml `



### Cleanup host
To retest if Ansible scripts working fine we can rebuild the server from Hetzner. The
base image is Ubuntu 22.04 LTS. 



Steps to cleanup: 
1. Go in `Hetzner cloud` to the server 
2. Click tab `rebuild`
3. Rebuild server with `Ubuntu 22.04 LTS`
4. Wait till server is rebuild
5. Go to tab `Rescue`
6. Click on `reset root password`
7. Copy root password to `inventory` file


After cleanup we need also to reset the root password
of the server manual and paste the new one in inventory.ini

If you want` to rerun the ansible scripts on the same server after it is rebuilding
we need to remove the IP from known hosts:

Remove the outdated host in ~/.ssh/know_hosts

```
vi ~/.ssh/known_hosts
remove the previously added host with crtl+k
save with crtl+x
```
