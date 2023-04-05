---
title: Characterisation Virtual Laboratory (CVL)
---

# Access CVL desktops

* [With University of Sydney login](#have-a-university-of-sydney-login)
* [Without University of Sydney login](#without-a-university-of-sydney-login)

# Have a University of Sydney login

Requirements:
* [Ronin Link client](https://blog.ronin.cloud/ronin-link/){:target="_blank"} installed 

To login to Ronin follow the [Ronin instructions]({% link _training/ronin.md %}){:target="_blank"}

Once logged in select the `VIRTLABTRAIN` (VIRTUAL LAB TRAINING) project

![Ronin project dashboard](/assets/img/Ronin VirtLabTrain project dashboard.png){:class="img-responsive"}

## Create a CVL desktop instance and connect

1. From the main Ronin menu select `Machines` and `New Machine` to bring you to the Machine composer
2. From `Step 1 - Software` scroll down to `Pre-configured software`
   * Select `CVL Desktop (Focal 20.04)`
   * *NB:* There is a Jammy 22.04 variant, however this version does not work with the 'Ronin Link' client
3. From `Step 2 - Address` type in a unique name such as `cvl-YOURNAME` the UI will indicate if the name is available
   * The FQDN of your machine will be `cvl-YOURNAME.sydneyuni.cloud`
4. From `Step 3 - Machine type` scroll down to `GPU Machines` and expand the menu
   * Scroll down to `G4DN` and select `g4dn.xlarge`
5. From `Step 5 - Storage`, this is optional and not required for this training session

On the right of the screen you should see something similar to the image bellow. Once you have reviewed the
instance creation details click on the `rocket` symbol to continue.

![Ronin machine instance summary](/assets/img/Ronin VM summary.png){:class="img-responsive"}

You will then be taken to the `Manage your Linux SSH key` pop up window.

6. Click on the `Create tab`, unless you already have created an SSH key
7. From the `Create tab` enter a unique name for your SSH key and click on `Generate a new SSH key`
   * A good practice is to use your email address for the key name e.g., dean.taylor@sydney.edu.au

![Ronin manage your SSH key](/assets/img/Ronin manage your SSH key.png){:class="img-responsive"}

8. Click on `Launch my machine`

![Ronin Good job](/assets/img/Ronin Good job.png){:class="img-responsive"}

9. Feel free to check out the links on this screen and once you are ready to continue, select `Show me my new machine`
   * This will take you to the `Project machines` page
   * You may need to scroll down, however you should see your machine in the `running` state

## Control your VM instance

Under the status line which should have the 'running' status showing you can see four tab icons.
* Select each tab icon to get an overview of what each is for. They are reasonably self explanatory.

From left to right
1. Information
2. Costing overview and options
3. VM scheduling
   1. While in this tab select `Smart schedule`
   2. Once you have looked at the available options, select `Stop only enabled`, located at bottom
   3. Ensure the VM is powered off by 5pm each day 
   4. Click on `Save changes`
4. Configuration

![Ronin smart scedule](/assets/img/Ronin smart schedule.png){:class="img-responsive"}

## Open with Ronin Link

Once back on the `Project machines` screen, you should be looking at your newly created machine.

If you are a Mac M1 or Linux user you will need to create a network route via the AnyConnect VPN to access your machine.
Please review [Mac M1 ARM64 and Linux client issues]({% link _training/ronin.md %}#mac-m1-arm64-and-linux-client-issues){:target="_blank"}.
If you have added the script to your PATH you can run the following command `ronin-client-setup <YOUR-MACHINE-NAME-HERE>`.

Click on the `Open with Ronin link`, if everything goes as expected you should see the Ronin Link application with a reference to your new machine.

![Ronin Link new machine card.png](/assets/img/Ronin Link new machine card.png){:class="img-responsive"}

Select `Connect to machine` and then select `Desktop`.

You will receive a message to `Install DCV`. Select `Install it!`. Please be patient as the software is installed.

*NB:* While you are waiting you can review the details of the AWS [NICE DCV software](https://aws.amazon.com/hpc/dcv/){:target="_blank"}.

# Without a University of Sydney login

Requirements:
* an SSH client
* Windows remote desktop (RDP) client

Your instructor will provide you with:
* the fully qualified domain name (FQDN) or internet protocol (IP) of your CVL desktop virtual machine (VM)
* authentication instructions required to access your assigned desktop

You will be able to follow along with the desktop and practical sections on the VM provided once the short intro
to Ronin is complete.

Once you have the External DNS and user credentials of your allocated virtual machine (VM).

Connect to the VM via SSH and tunnel specific ports. These ports will be used during the training session.

```bash
# Connect to the VM via SSH and tunnel specific ports
# Port 3389 is the Windows Remote Desktop (RDP) port
# Replace the FQDN with the one provided
ssh \
  -Llocalhost:3389:localhost:3389 \
  -Llocalhost:8080:localhost:8080 \
  -Llocalhost:8888:localhost:8888 \
  ubuntu@ec2-13-238-182-54.ap-southeast-2.compute.amazonaws.com
```

Once connected via SSH to your VM instance you should ensure that the setup process has completed before continuing.

```bash
cloud-init status --wait
```

![RDP connection](/assets/img/RDP connect 2 CVL desktop.png){:class="img-responsive"}

