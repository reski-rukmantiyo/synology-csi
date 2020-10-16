# synology-csi ![Docker image](https://github.com/jparklab/synology-csi/workflows/Docker%20image/badge.svg) ![Go Report Card](https://goreportcard.com/badge/github.com/jparklab/synology-csi)

A [Container Storage Interface](https://github.com/container-storage-interface) Driver for Synology NAS.

# Platforms Supported

 The driver supports linux only since it requires `iscsiadm` to be installed on the host. It is currently tested with Ubuntu 16.04, Ubuntu 18.04, and [Alpine](https://alpinelinux.org/).

 We have pre-built Docker [images](https://hub.docker.com/r/jparklab/synology-csi) for amd64, arm64, armv7 architectures.

# Quickstart Guide

## Synology Configuration:

More on Synology details in docs folder


## Synology CSI Configuration and Setup

 1. Clone the repository
 2. Perform the following on all your Kubernetes cluster nodes (Ubuntu):
   * Install the build dependencies: `apt-get update && apt-get install -y open-iscsi make && snap install go --classic`
   * Change (`cd`) to the cloned repository
   * Run the Makefile build: `make`
   * Create the kubelet plugins directory: `mkdir -p /var/lib/kubelet/plugins/csi.synology.com/`
   * Copy the built binary from `<cloned repository path>/bin/` to the kubelet plugin directory: `cp ./bin/synology-csi-driver /var/lib/kubelet/plugins/csi.synology.com/`
 3. Edit the appropriate deployment for your Kubernetes version as needed
   * You will probably want to adjust the `storage_class.yml` based on your Synology volume, filesystem, and provisioning type (thick/thin) and additionally the name of the class as well to meet your requirements/preferences.
   * You may also have to build containers depending on your hardware architecture and adjust the container images in the deployments.  This will involve building the container images for this project and the following projects:  
       - https://github.com/kubernetes-csi/node-driver-registrar
       - https://github.com/kubernetes-csi/external-provisioner
       - https://github.com/kubernetes-csi/external-attacher
 4. Create a [syno-config.yml](syno-config.yml)
 5. Create the namespace: `kubectl create ns synology-csi`
 6. Create a secret from the customized [syno-config.yml](syno-config.yml): `kubectl create secret -n synology-csi generic synology-config --from-file=syno-config.yml`
 7. Apply the deployment: `kubectl apply -f deploy/<kubernetes version>`

 At this point you should be able to deploy persistent volume claims with the new storage class.



# Documentation

More Documentations is on Docs Folder