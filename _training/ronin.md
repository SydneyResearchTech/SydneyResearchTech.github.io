---
---

# Linux and Mac M1 (ARM64) client issues

## AnyConnect VPN

```bash
#!/usr/bin/env bash
# WORKAROUND AnyConnect client for Linux and Mac M1 ARM64 do not add dynamic
# routes to the VPN. This method is used to control access to Ronin resources.
#
DOMAIN='sydneyuni.cloud'
GW='10.48.0.242'

# Resolve Machine name to IP address
# Remove old ssh host keys
if [[ $# -gt 0 ]]; then
        MACH_NAME="${1}"
        FQDN="${MACH_NAME}.${DOMAIN}"

        IP="$(dig +short ${FQDN} @1.1.1.1)"
        [ -z $IP ] && { >&2 echo "$FQDN does not resolve"; exit 127; }

        ssh-keygen -R ${FQDN} || true
fi      

# Add Ronin UI and Machine route via VPN
if which ip &>/dev/null; then
        sudo ip route add 13.54.246.143/32 via ${GW}
        [ -z $IP ] || sudo ip route add ${IP}/32 via ${GW}
else
        sudo route add 13.54.246.143/32 ${GW}
        [ -z $IP ] || sudo route add ${IP}/32 ${GW}
fi

# Connect via SSH and tunnel RDP
if [[ ! -z $FQDN ]]; then
        echo ssh -Llocalhost:63389:localhost:3389 ubuntu@${FQDN}
        exec ssh \
                -i ${HOME}/.ssh/ronin-id_ed25519 \
                -L localhost:63389:localhost:3389 \
                -o StrictHostKeyChecking=no \
                -o UserKnownHostsFile=/dev/null \
                ubuntu@${FQDN}
fi      
```
