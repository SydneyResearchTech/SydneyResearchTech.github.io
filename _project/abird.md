---
title: The Australian Biomedical Imaging Research Database (ABIRD)
---

## Nimbus (OpenStack) project setup

### Configure OpenStack CLI credentials for your workstation

*TODO* Change this to GUI
```bash
openstack application credential create \
 --role Member \
 --description 'User account general access' \
 abird-YOUR@EMAIL.ADDRESS
```

Add the ID and secret to the fields bellow to create your local authentication file.

```yaml
# ${HOME}/.config/openstack/clouds.yaml
#
clouds:
  abird:
    auth:
      auth_url: https://keystone.rc.nectar.org.au:5000/v3/
      application_credential_id: ##########
      application_credential_secret: ##########
    region_name: "Melbourne"
    interface: "public"
    identity_api_version: 3
    auth_type: "v3applicationcredential"
```

### Configure the OpenStack project and fill out the HEAT template (HOT) local deployment settings. 

```bash
export OS_CLOUD=abird

# Create an SSH key pair if not already present
openstack keypair create \
 --public-key ${HOME}/.ssh/id_rsa.pub --type ssh \
 dean_taylor_sydney_edu_au

# Select the location for your deployment from the list provided 
openstack availability zone list

# Not a currently supported NeCTAR feature; under review
openstack subnet pool create \
 --default-prefix-length 24 \
 --min-prefix-length 20 \
 --max-prefix-length 28 \
 --description "internal IPv4 address pool" \
 --default \
 --pool-prefix 10.39.0.0/16 \
 rfc1918

openstack stack create -e ~/abird/monash.env.yaml -t https://raw.githubusercontent.com/SydneyResearchTech/heat-templates/main/microk8s.hot.yaml k8s
```

```yaml
---
# abird/monash.env.yaml
parameters:
  availability_zone: monash-03
  dns_domain: abird.cloud.edu.au
  flavor: "m3.xlarge"
  server_count: 3
  timezone: "Australia/Melbourne"
  image: "NeCTAR Ubuntu 22.04 LTS (Jammy) amd64"
  key_name: dean.taylor@sydney.edu.au
  network_external: "monash-03-public"
  security_group_rules:
  - {port_range_max: 80, port_range_min: 80, protocol: tcp, remote_ip_prefix: "0.0.0.0/0"}
  - {port_range_max: 443, port_range_min: 443, protocol: tcp, remote_ip_prefix: "0.0.0.0/0"}
  subnet_pool: rfc1918

resource_registry:
  "OS::Custom::Server": https://raw.githubusercontent.com/SydneyResearchTech/heat-templates/main/server.hot.yaml
```

## Ansible

```bash
python3 -m pip install --user ansible
ansible-galaxy collection install -U openstack.cloud
```
