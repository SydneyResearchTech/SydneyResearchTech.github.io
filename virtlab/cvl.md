---
layout: page
title: CVL
---

## [Characterisation Virtual Laboratory]({% link project/accs.md %})

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

# Add software to the system PATH
cat <<'EOT' |sudo tee /etc/profile.d/Z99-cvl.sh
export PATH="/vol/cvl/bin:${PATH}"
EOT
```

### Software build CI/CD

*NB:* It is recommended to use the USyd CVL desktop image to initiate the CICD software build process.

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
