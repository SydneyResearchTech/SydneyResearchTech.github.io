---
title: MIRC-CTP transfer to XNAT
---

Deploy MIRC CTP

* https://github.com/SydneyResearchTech/charts

```bash
helm repo add restek https://sydneyresearchtech.github.io/charts/
helm repo update
helm search repo restek/

helm show values restek/mirc-ctp
```

Create X.509 certificate request for signing

```bash
kubectl create ns mirc-ctp

kubectl -nmirc-ctp create secret generic mirc-ctp-keystore --from-literal=keystorePassword=$(openssl rand -base64 30)

cat <<EOT |kubectl apply -f -
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: mirc-ctp-keystore
  namespace: mirc-ctp
spec:
  secretName: mirc-ctp-keystore-tls
  duration: 2160h # 90d
  renewBefore: 360h # 15d
  subject:
    organizations:
      - The University of Sydney
  # The use of the common name field has been deprecated since 2000 and is
  # discouraged from being used.
  commonName: mirc-ctp.$(hostname --fqdn)
  isCA: false
  privateKey:
    algorithm: RSA
    encoding: PKCS1
    size: 2048
  usages:
    - server auth
  dnsNames:
    - mirc-ctp.$(hostname --fqdn)
    - mirc-ctp.default.svc.cluster.local
    - mirc-ctp.default.svc
    - mirc-ctp.default
    - mirc-ctp
    - localhost
  issuerRef:
    name: selfsigned-cluster-issuer
    kind: ClusterIssuer
    group: cert-manager.io
  keystores:
    pkcs12:
      create: true
      passwordSecretRef:
        name: "mirc-ctp-keystore"
        key: "keystorePassword"
EOT
```

Create local values.yaml for deployment
```bash
HOST=$(hostname --fqdn)
XNATDCMNodePort=30002

cat <<EOT > ctp-values.yaml
global:
  storageClass: ""
Configuration:
    Pipeline:
    - '@name': Main Pipeline
      DicomImportService:
         '@name': DicomImportService
         '@id': DicomImportService0
         '@class': org.rsna.ctp.stdstages.DicomImportService
         '@root': /scratch/root/dicom-import
         '@port': 1104
      DicomExportService:
         '@class': org.rsna.ctp.stdstages.DicomExportService
         '@name': "DicomExportService"
         '@quarantine': /scratch/quarantine/DicomExportService
         '@root': /scratch/root/DicomExportService
         '@url': dicom://XNAT:TEST-CTP@$HOST:$XNATDCMNodePort
    Server:
      '@ssl': 'no'
services:
  type: NodePort
ingress:
  enabled: true
  className: nginx
  hosts:
    - host: ctp.$HOST
      paths:
        - path: /
          pathType: ImplementationSpecific
resources:
  limits:
    cpu: 1000m
    memory: 2000Mi
  requests:
    cpu: 100m
    memory: 2000Mi
EOT

# Helm install
helm upgrade mirc-ctp restek/mirc-ctp -n mirc-ctp -i --create-namespace -f ctp-values.yaml
```

Testing:
```bash
# Add the CTP administrative web interface domain name to /etc/hosts 
sudo tee -a /etc/hosts > /dev/null << EOT
127.0.0.1 ctp.$HOST
EOT

# Install DCMTK on your VM
sudo apt install dcmtk -y

# Find out the node port which CTP listens on
kubectl get svc -n mirc-ctp

# Simulate sending DICOM as an instrument
# Replace XXXXX with the node port for service mirc-ctp-dicomimportse-0 as in above step
storescu -v -aec local-instrument -aet local-ctp localhost XXXXX /vol/eresearch2023/DICOM/samples/CR-MONO1-10-chest.dcm

# Verify the sample DICOM file is received in XNAT's prearchive
```

