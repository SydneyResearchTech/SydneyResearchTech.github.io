---
title: Development howto's and recomendations
draft: false
---

## Kubernetes

### DNS

Align the local host and microk8s DNS resolution for testing domain resolution.
Add entries into `/etc/hosts.dnsmasq` and SIGHUP dnsmasq with `sudo systemctl reload dnsmasq.service` to provide DNS service resolution to your local development system.
An alternative is to use the `nip.io` service provider.

```bash
## DNSMasq DNS server setup
sudo apt -y install dnsmasq

sudo touch /etc/hosts.dnsmasq

cat <<EOT |sudo tee /etc/dnsmasq.d/conf
interface=ens5
domain-needed
filterwin2k
resolv-file=/run/systemd/resolve/stub-resolv.conf
local=/lo.sydney.edu.au/
bind-interfaces
no-hosts
addn-hosts=/etc/hosts.dnsmasq
expand-hosts
domain=lo.sydney.edu.au
EOT

## DNS client resolution configuration

# Get interface IP address
IPADDR=$(ip -4 addr show dev ens5 scope global |sed -n 's/\s*inet\s*\([0-9.]*\).*$/\1/p')

sudo unlink /etc/resolv.conf

cat <<EOT |sudo tee /etc/resolv.conf
nameserver $IPADDR
options edns0 trust-ad
search ap-southeast-2.compute.internal
EOT

## Kubernetes forwarder setting update to local DNS

# On microk8s installation
microk8s enable dns:${IPADDR}

# POST microk8s installation
microk8s kubectl -n kube-system edit configmap/coredns
# edit and change `forward . /etc/resolv.conf` to `forward . 10.0.19.23` replacing example IP with $IPADDR set above

kubectl rollout restart deployment coredns -nkube-system
```

## OIDC

[KeyCloak for development and testing.](https://www.keycloak.org/getting-started/getting-started-kube)

```bash
# Add address to local DNS resolution
IPADDR=$(ip -4 addr show dev ens5 scope global dynamic up |sed -n 's;\s*inet\s*\([0-9./]*\).*$;\1;p')
echo "$IPADDR oidc.localhost" |sudo tee -a /etc/hosts

# Deploy KeyCloak development
kubectl create -f https://raw.githubusercontent.com/keycloak/keycloak-quickstarts/latest/kubernetes/keycloak.yaml

# Add Ingress to KeyCloak
wget -q -O - https://raw.githubusercontent.com/keycloak/keycloak-quickstarts/latest/kubernetes/keycloak-ingress.yaml | \
sed "s/KEYCLOAK_HOST/oidc.localhost/" | \
kubectl create -f -
```

The URL to connect to KeyCloak is `http://oidc.localhost/` with credentials `admin` and password `admin`
