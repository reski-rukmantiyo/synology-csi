# Install Details

Make sure that `iscsiadm` is installed on all the nodes where you want this attacher to run.


```

# Deploy

## Ensure Kubernetes Cluster is Configured for CSI Drivers

  For Kubernetes v1.12, and v1.13, feature gates need to be enabled to use CSI drivers.
  Follow instructions on https://kubernetes-csi.github.io/docs/csi-driver-object.html and https://kubernetes-csi.github.io/docs/csi-node-object.html
  to set up your Kubernetes cluster.

## Create a config file <a name='config'></a>

```yaml
---
# syno-config.yml file
host: <hostname>           # ip address or hostname of the Synology NAS
port: 5000                 # change this if you use a port other than the default one
sslVerify: false           # set this true to use https
username: <login>          # username
password: <password>       # password
loginApiVersion: 2         # Optional. Login version. From 2 to 6. Defaults to "2".
loginHttpMethod: <method>  # Optional. Method. "GET", "POST" or "auto" (default). "auto" uses POST on version >= 6
sessionName: Core          # You won't need to touch this value
enableSynoToken: no        # Optional. Set to 'true' to enable syno token. Only for versions 3 and above.
enableDeviceToken: yes     # Optional. Set to 'true' to enable device token. Only for versions 6 and above.
deviceId: <device-id>      # Optional. Only for versions 6 and above. If not set, DEVICE_ID environment var is read.
deviceName: <name>         # Optional. Only for versions 6 and above.
```


## Create a Secret from the syno-config.yml file

    kubectl create secret -n synology-csi generic synology-config --from-file=syno-config.yml

### (Optional) Use https with self-signed CA

  To use https with certificate that is issued by self-signed CA. CSI drivers needs to access the CA's certificate.
  You can add the certificate using configmap.

  Create a configmap with the certificate

```bash
# e.g.
##  kubectl create configmap synology-csi-ca-cert --from-file=self-ca.crt
kubectl create configmap synology-csi-ca-cert --from-file=<ca file>
```

  Add the certificate to the deployments

```yaml
# Add to attacher.yml, node.yml, and provisioner.yml
..
spec:
...
- name: csi-plugin
...
  volumeMounts:
  ...
  - mountPath: /etc/ssl/certs/self-ca.crt
    name: cert
    subPath: self-ca.crt      # this should be the same as the file name that is used to create the configmap
...
volumes:
- configMap:
    defaultMode: 0444
    name: synology-csi-ca-cert
```

## Deploy to Kubernetes

```bash
kubectl apply -f deploy/kubernetes/v1.15
```

### Parameters for the StorageClass and Synology

By default, iscsi LUN will be created on Volume 1 (`/volume1`) location with thin provisioning.
You can set parameters in `storage_class.yml` to choose different locations or volume type.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
name: synology-iscsi-storage
...
provisioner: csi.synology.com
parameters:
  location: '/volume2'
  type: 'FILE'          # if the location has ext4 file system, use FILE for thick provisioning, and THIN for thin provisioning.
                        # for btrfs file system, use BLUN_THICK for thick provisioning, and BLUN for thin provisioning.
reclaimPolicy: Delete
```

***NOTE:*** if you have already created storage class, you would need to delete the storage class and recreate it.

