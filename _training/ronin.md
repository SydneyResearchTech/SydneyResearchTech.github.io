---
---

# Login

* Initiate AnyConnect VPN client service to The University of Sydney network
  * If you are a Mac M1 (ARM64) or Linux user please see the [notes bellow](#mac-m1-arm64-and-linux-client-issues).
* Navigate to the [Ronin login page](https://ronin.sydneyuni.cloud/login.php)
* Click on `LET'S GO!`

![https://ronin.sydneyuni.cloud/login.php](/assets/img/Ronin login USyd.png){:class="img-responsive"}

* Enter your credentials and login

![Ronin projects page](/assets/img/Ronin projects.png){:class="img-responsive"}

# Mac M1 ARM64 and Linux client issues

## AnyConnect VPN

*ISSUE:* The University of Sydney AnyConnect VPN service uses dynamic routing for the Ronin service which under
normal operation provides your machine with network routing instructions to connect to the Ronin user interface (UI) and any VM's
that are created via Ronin. Unfortunately the AnyConnect client for Mac M1 and Linux do not honour this instruction.

*WORKAROUND:* The script bellow will provide routing instructions for both the Ronin UI and any VM's you specify by the
name provided. Please make any modifications you require or you can utilise this script to enter the route information
by hand.

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

