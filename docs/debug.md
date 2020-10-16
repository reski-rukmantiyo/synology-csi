# Test

Here we use [gocsi](https://github.com/rexray/gocsi) to test the driver.

## Create a Configuration File for Testing

You need to create a config file that contains information to connect to the Synology NAS API. See [Create a config file](#config) below

## Start Plugin Driver

```bash
# You can specify any name for nodeid
go run cmd/syno-csi-plugin/main.go \
  --nodeid CSINode \
  --endpoint tcp://127.0.0.1:10000 \
  --synology-config syno-config.yml
```

## Get plugin info

```bash
csc identity plugin-info -e tcp://127.0.0.1:10000
```

## Create a volume

```bash
csc controller create-volume \
  --req-bytes 2147483648 \
  -e tcp://127.0.0.1:10000 \
  test-volume \
"8.1" 2147483648 "iqn"="iqn.2000-01.com.synology:kube-csi-test-volume" "mappingIndex"="1" "targetID"="8"
```

## List Volumes

The first column in the output is the volume D

```bash
csc controller list-volumes -e tcp://127.0.0.1:10000
"8.1" 2147483648 "iqn"="iqn.2000-01.com.synology:kube-csi-test-volume" "mappingIndex"="1" "targetID"="8"
```

## Delete the Volume

```bash
# e.g.
## csc controller delete-volume  -e tcp://127.0.0.1:10000 8.1
csc controller delete-volume  -e tcp://127.0.0.1:10000 <volume id>
```