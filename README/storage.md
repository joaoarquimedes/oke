# Storage

Procedimentos para disponibilizar tipos de StorageClass para criação de forma dinâmica dos discos no OCI.

- [Documentação](https://docs.oracle.com/pt-br/iaas/Content/ContEng/Tasks/contengcreatingpersistentvolumeclaim_topic-Provisioning_PVCs_on_BV.htm)

**StorageClass de baixa performance**: 0 VPUs - XFS
```
kubectl apply -f storage/oci-bv-0vpus-xfs.yml
```

**StorageClass balanceado**: 10 VPUs - XFS
```
kubectl apply -f storage/oci-bv-10vpus-xfs.yml
```

**StorageClass de alta performance**: 20 VPUs - XFS
```
kubectl apply -f storage/oci-bv-20vpus-xfs.yml
```

**StorageClass de baixa performance**: 0 VPUs - EXT4
```
kubectl apply -f storage/oci-bv-0vpus-ext4.yml
```

**StorageClass balanceado**: 10 VPUs - EXT4
```
kubectl apply -f storage/oci-bv-10vpus-ext4.yml
```

**StorageClass de alta performance**: 20 VPUs - EXT4
```
kubectl apply -f storage/oci-bv-20vpus-ext4.yml
```

**StorageClass para discos do tipo NFS (FSS)**<br>
Criado políticas no IAM:
```
ALLOW any-user to manage file-family in compartment prd-oke where request.principal.type = 'cluster'
ALLOW any-user to use virtual-network-family in compartment prd-oke where request.principal.type = 'cluster'
ALLOW any-user to manage file-family in TENANCY where request.principal.type = 'cluster'
ALLOW any-user to use virtual-network-family in TENANCY where request.principal.type = 'cluster'

```
Aplicando StorageClass:
```
kubectl apply -f storage/oci-fss.yml
```
<br><br>


## Storage Backup

[Documentação](https://docs.oracle.com/pt-br/iaas/Content/ContEng/Tasks/contengcreatingpersistentvolumeclaim_topic-Provisioning_PVCs_on_BV.htm#contengcreatingpersistentvolumeclaim_topic-Provisioning_PVCs_on_BV-PV_From_Snapshot_CSI)

Para criação e uso do serviço de backup, há necessidade de instalação dos recursos de CRD (Custom Resource Definition) para execução dos objetos de `VolumeSnapshot`, `VolumeSnapshotContent` e `VolumeSnapshotClass`.

Comandos:

VolumeSnapshotClass:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml
```

VolumeSnapshotContent:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotcontents.yaml
```

VolumeSnapshot:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshots.yaml
```
<br>

Criando definições para reivindicação do backup:

Full:
```
kubectl apply -f storage/oci-bv-bkp-full.yml
```

Incremental:
```
kubectl apply -f storage/oci-bv-bkp-incr.yml
```
