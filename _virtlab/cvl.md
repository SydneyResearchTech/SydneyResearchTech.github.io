---
title: Characterisation Virtual Laboratory (CVL)
---

The official upstream [ACCS Work Package 1 (WP1)]({% link _project/accs.md %}){:target="_blank"} presents an array of services providing access to virtual desktop resources for research groups within characterisation space. The University of Sydney Research Technology team has adapted a number of these services to suit institutional resourcing and integration into existing services.

The primary difference is the focus on service oriented cloud based artifacts. At this time the University is focusing on public cloud and as you will see the outputs are targeted at producing Amazon related resources. Some of the outcomes associated with this design principal are:
* Implementation of continuous development / continuous delivery (CI/CD) to reduce time and resources taken to provide services all while working towards a ever higher quality user experience.
* Multiple levels of services produced to allow the maximum flexibility and high reuse of existing resources. This can help to provide research outcomes fit for purpose and in alignment with project goals.
* Reduced cost under many workload types using the most optimal payment to fit work practices. E.g., At this time it costs around $0.70 p/h to run a medium spec GPU virtual desktop with 3D graphics acceleration suited to visualisation of research data.
* Access of resources from any location. At this time the research cloud (Ronin) can be accessed from any location via the university VPN.
* Common research platform. This has many advantages including helping with collaboration, reliable software stacks, consistent support, predictable user experience, etc. 

## CVL @The University of Sydney

### Access

CVL at The University of Sydney has been incorporated into the main Research Cloud infrastructure and can be accessed via the [Researcher cloud portal (RONIN)](https://ronin.sydneyuni.cloud).

## Artifacts

### Amazon Machine Image (AMI)

The University of Sydney's CVL image is publicly available to any AWS account within the `ap-southeast-2` region. To find out the latest AMI ID or details of the currently available public images see the instructions bellow.

```bash
# To get the details of all available AMIs
aws ec2 describe-images --owners 381427642830

# To get the latest AMI ID for a specific image
aws ec2 describe-images --owners 381427642830 \
 --filters "Name=name,Values=ubuntu-jammy-22.04-amd64-server-cvl-desktop-*" \
 --query 'reverse(sort_by(Images,&CreationDate))[0].ImageId' \
 --output text
```

Using the AMI within your own AWS account.

Prerequisite:
* [AWS Command Line Interface (CLI)](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

```bash
# get AMI ID of the latest CVL image
AMI_ID=$( \
  aws ec2 describe-images --owners 381427642830 \
    --filters 'Name=name,Values=ubuntu-jammy-22.04-amd64-server-cvl-desktop-*' \
    --query 'reverse(sort_by(Images,&CreationDate))[0].{id:ImageId}' \
    --output text)

# without AWS default VPC. Get an available VPC Id and Subnet Id with the largest free IP range available.
aws ec2 describe-subnets \
  --filters 'Name=map-public-ip-on-launch,Values=true' \
  --query 'reverse(sort_by(Subnets,&AvailableIpAddressCount))[0].{SubnetId: SubnetId, VpcId: VpcId}'

# Start CVL Desktop stack
aws cloudformation create-stack --stack-name cvl-desktop --template-body file://cvl-desktop.cf.yaml \
  --parameters "ParameterKey=ImageId,ParameterValue=${AMI_ID}"

# Verify Stack creation process is operating as expected
aws cloudformation describe-stack-events --stack-name cvl-desktop

# Display final stack details once created
aws cloudformation describe-stacks --stack-name cvl-desktop
```

AWS CloudFormation template (cvl-desktop.cf.yaml), modify the template to suit your own environment and requirements.

```yaml
{% include cvl-desktop.cf.yaml %}
```

CloudFormation template references:
* [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)
* [Cloud-init](https://cloudinit.readthedocs.io/en/latest/#)
  * `UserData` section
  * Used to perform bootstrap operations on the image to ensure essential services and packages are available early in the instances startup process.

### Software library for research applications

The research applications are containerised utilising [Apptainer](https://apptainer.org) and are presented as Singularity Image File (SIF). The software library can be presented to any Linux workstation via an [S3 File System in User Space (FUSE)](https://github.com/s3fs-fuse/s3fs-fuse) mount.

*NB:* Currently USyd AWS accounts do not allow public S3 access. At this time a read-only account has been provided to allow access to the software library S3 bucket.

```bash
# Install the S3 FUSE package
sudo apt-get update
sudo apt-get -y install s3fs

# WORKAROUND: The S3 bucket is not publicly available. This requires the addition of credentials for read-only access.
# Add AWS profile and credentials to the root account
cat <<EOT |sudo tee -a /root/.aws/config
[cvlapps]
region=ap-southeast-2
output=json
EOT
cat <<EOT |sudo tee -a /root/.aws/credentials
[cvlapps]
aws_access_key_id=XXXXXXXXXXXXXXXXXXXX
aws_secret_access_key=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
role_arn=arn:aws:iam::381427642830:role/cvl-desktop-s3-readOnly-924D5598-47E1-40CD-A925-92E712EE0141
source_profile=cvlapps
EOT

# Add the mount entry
cat <<EOT |sudo tee -a /etc/fstab
cvl-desktop-applications-x86-64 /vol/cvl fuse.s3fs x-systemd.automount,_netdev,nofail,allow_other,gid=100,use_cache=/scratch/s3fs,del_cache,profile=cvlapps,enable_noobj_cache,url=https://s3.ap-southeast-2.amazonaws.com,endpoint=ap-southeast-2,ro 0 0
EOT

# Update systemd and initiate the mount
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target

# Optional. Add software to the system PATH
cat <<'EOT' |sudo tee /etc/profile.d/Z99-cvl.sh
export PATH="/vol/cvl/bin:${PATH}"
EOT

# Optional. Add XDG configuration to the hosts settings to display CVL software within the standard Application menu.
cat <<EOT |sudo tee -a /etc/environment
XDG_CONFIG_DIRS="/vol/cvl/config:/etc/xdg"
EOT
```

S3FS provides a number of functions in the delivery of the software stack.
* Authentication to the S3 bucket. This can be provided via a direct client configuration containing authentication details or via credentials assigned to the host via the preferred method of using an [Amazon EC2 Instance Profile](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html).
* POSIX compliant storage representation.
* Local cache of content to ensure active resources are performant. The USyd CVL instance utilises a solid state drive mounted as `/scratch' to provide performant Iops once the S3 object (SIF image) has been cached locally. This imposes a single delay in the initial download on user demand and then ongoing access to a locally cached resource.

### Software build CI/CD

*NB:* It is recommended to use the USyd CVL desktop image to initiate the CI/CD software build process as it contains all the pre-requisite software and the S3FS mount (read/write) of the software library.

```bash
# Clone repository code
git clone --branch usyd https://github.com/SydneyResearchTech/CharacterisationVL-Software.git
cd CharacterisationVL-Software

# Build all updated software definitions from last build process
make

# Generate the XDG menus and application entries
make xdg
```

Currently within a Forked version of the upstream project [CharacterisationVL-Software](https://github.com/SydneyResearchTech/CharacterisationVL-Software/tree/usyd), there is the addition of:
* Makefile
  * `make` will build all Apptainer definition files within the `/defs` folder.
  * `make xdg` will create XDG desktop menus for all binary files within the `/vol/cvl/bin` (default) directory.
* defs/
  * Updated Apptainer definition files for software builds.
* bin/
  * Helper and operational scripts.
* bin/xdg-desktop-menu
  * Python3 script to generate XDG desktop menus and application entries.
  * this script is a component part of the Makefile initiated via the `make xdg` operation and does not need to be run independently.

*NB:* If not using the USyd CVL desktop image for the build process, see the [defs/README.md](https://github.com/SydneyResearchTech/CharacterisationVL-Software/blob/usyd/defs/README.md) for details of how to alter setting defaults to match your environment.
