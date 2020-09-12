---
title: AKS (Azure Kubernetes Service)에서 Azure 디스크용 CSI (Container Storage Interface) 드라이버 사용
description: AKS (Azure Kubernetes Service) 클러스터에서 Azure 디스크에 CSI (Container Storage Interface) 드라이버를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422052"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service) (미리 보기)에서 Azure CSI (disk Container Storage Interface) 드라이버 사용
Azure Disk CSI 드라이버는 AKS에서 Azure 디스크의 수명 주기를 관리 하는 데 사용 하는 [CSI 사양](https://github.com/container-storage-interface/spec/blob/master/spec.md) 규격 드라이버입니다. 

CSI (Container Storage Interface)는 Kubernetes에서 임의 블록 및 파일 저장소 시스템을 컨테이너 화 된 작업에 노출 하는 표준입니다. AKS (Azure Kubernetes Service)는 CSI을 채택 하 고 사용 하 여 핵심 Kubernetes 코드를 터치 하 고 릴리스 주기를 기다릴 필요 없이 Kubernetes에서 새로운 기존 저장소 시스템을 제공 하거나 새로 제공 하는 플러그 인을 작성, 배포 및 반복할 수 있습니다.

CSI driver를 지 원하는 AKS 클러스터를 만들려면 [Azure 디스크에 대 한 CSI 드라이버 사용 및 AKS에서 Azure Files](csi-storage-drivers.md)을 참조 하세요.

>[!NOTE]
> *"트리 내 드라이버"* 는 코어 kubernetes 코드 및 플러그 인 새 CSI 드라이버의 일부인 현재 저장소 드라이버를 나타냅니다.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Azure 디스크에 CSI 영구 볼륨 (PV) 사용 

[영구적 볼륨](concepts-storage.md#persistent-volumes) 은 Kubernetes pod와 함께 사용 하기 위해 프로 비전 된 저장소 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 한 Pod에 사용할 Azure 디스크가 포함된 영구 볼륨을 동적으로 만드는 방법을 설명합니다. 정적 프로 비전의 경우 [Azure 디스크를 사용 하 여 볼륨 수동 만들기 및 사용](azure-disk-volume.md)을 참조 하세요.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>기본 제공 저장소 클래스를 사용 하 여 동적으로 Azure disk PVs 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 스토리지 클래스에 대한 자세한 내용은 [Kubernetes 스토리지 클래스][kubernetes-storage-classes]를 참조하세요. AKS에서 storage CSI 드라이버를 사용 하 `StorageClasses` 는 경우 **AZURE Disk CSI storage 드라이버**를 활용 하는 2 개의 추가 기본 제공 됩니다. 추가 CSI 저장소 클래스는 클러스터에서 트리 내 기본 저장소 클래스와 함께 생성 됩니다.

>[!NOTE]
> *"트리 내 드라이버"* 는 플러그 인 core kubernetes CODE 및 CSI 드라이버의 일부인 현재 저장소 드라이버를 나타냅니다.

- `managed-csi` -Azure StandardSSD LRS (로컬 중복 저장소)를 사용 하 여 관리 디스크를 만듭니다.
- `managed-csi-premium` -Azure Premium LRS (로컬 중복 저장소)를 사용 하 여 관리 디스크를 만듭니다. 

두 저장소 클래스의 회수 정책은 각각의 영구적 볼륨이 삭제 될 때 기본 Azure 디스크가 삭제 되도록 합니다. 또한 저장소 클래스는 영구적 볼륨을 확장 가능 하도록 구성 합니다. 새 크기의 영구적 볼륨 클레임을 편집 하기만 하면 됩니다.

이러한 저장소 클래스를 활용 하려면 [PVC (영구적 볼륨 클레임)](concepts-storage.md#persistent-volume-claims) 및이를 참조 하 고 활용 하는 각 pod를 만들어야 합니다. PVC(영구적 볼륨 클레임)을 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비전합니다. PVC는 미리 만든 저장소 클래스 또는 사용자 정의 저장소 클래스 중 하나를 사용 하 여 원하는 SKU 및 크기에 대 한 Azure 관리 디스크를 만들 수 있습니다. Pod 정의가 만들어지면 원하는 스토리지를 요청하는 영구적 볼륨 클레임이 지정됩니다.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 예제 pod 및 각각의 영구적 볼륨 클레임을 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Pod가 실행 중 상태가 되 면 라는 새 파일을 만듭니다 `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

이제 다음 명령을 실행 하 고 출력에 파일이 표시 되는지 확인 하 여 디스크가 올바르게 탑재 되었는지 확인할 수 있습니다 `test.txt` . 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>사용자 지정 저장소 클래스 만들기

기본 저장소 클래스는 가장 일반적인 시나리오에 적합 하지만 모든 것은 아닙니다. 경우에 따라 고유한 매개 변수를 사용 하 여 고유한 저장소 클래스를 사용자 지정 해야 할 수 있습니다. Exemplify에는를 변경 하려는 시나리오가 있습니다 `volumeBindingMode` . 

기본 저장소 클래스는 PersistentVolumeClaim를 `volumeBindingMode: Immediate` 만든 후 즉시 발생 되도록 보장 하는를 사용 합니다. 노드 풀이 토폴로지 제약을 받는 경우 (예: 가용성 영역 사용), 영구 볼륨은 Pod의 일정 요구 사항 (이 경우 특정 영역에 포함 됨)에 대 한 지식 없이 바인딩하거나 프로 비전 됩니다.

이 시나리오를 해결 하기 위해 PersistentVolumeClaim를 `volumeBindingMode: WaitForFirstConsumer` 사용 하 여 Pod를 만들 때까지 PersistentVolume의 바인딩 및 프로 비전을 지연 하는를 사용할 수 있습니다. 이러한 방식으로 PV는 Pod의 일정 제약 조건으로 지정 된 가용성 영역 (또는 다른 토폴로지)에서 준수 하 고 프로 비전 됩니다. 

이라는 파일을 만들고 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 아래에 매니페스트를 붙여넣습니다.
저장소 클래스는 저장소 클래스와 동일 `managed-csi` 하지만 다른를 사용 `volumeBindingMode` 합니다. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 저장소 클래스를 만들고 파일을 지정 합니다 `sc-azuredisk-csi-waitforfirstconsumer.yaml` .

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>볼륨 스냅숏

Azure disk CSI 드라이버는 [영구적 볼륨의 스냅숏](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)만들기를 지원 합니다. 이 기능의 일부로 드라이버는 매개 변수에 설정 된 값 (기본적으로 true 임)에 따라 *전체* 또는 [ *증분* 스냅숏을](../virtual-machines/windows/disks-incremental-snapshots.md) 수행할 수 있습니다 `incremental` . 

모든 매개 변수에 대 한 자세한 내용은 [볼륨 스냅숏 클래스 매개 변수](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)를 참조 하세요.

### <a name="create-a-volume-snapshot"></a>볼륨 스냅숏 만들기

이 기능을 exemplify [kubectl apply][kubectl-apply] 명령을 사용 하 여 [볼륨 스냅숏 클래스](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) 를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

이제 [이 자습서의 시작 부분에서 동적으로 만든](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)PVC에서 [볼륨 스냅숏을](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) 만들어 보겠습니다 `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

스냅숏이 올바르게 만들어졌는지 확인 합니다.

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>볼륨 스냅숏을 기반으로 새 PVC 만들기

볼륨 스냅숏을 기반으로 새 PVC를 만들 수 있습니다. 이전 단계에서 만든 스냅숏을 사용 하 고 [새 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) 및 [새 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) 를 만들어 사용 합니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

마지막으로 콘텐츠를 확인 하 여 이전에 만든 동일한 PVC 인지 확인 합니다.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

예상 대로 이전에 만든 파일을 계속 볼 수 있습니다 `test.txt` .

## <a name="clone-volumes"></a>볼륨 복제

복제 된 볼륨은 기존 Kubernetes 볼륨의 복제로 정의 됩니다. Kubernetes에서 볼륨을 복제 하는 방법에 대 한 자세한 내용은 [볼륨 복제](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)에 대 한 개념 설명서를 참조 하세요.

Azure 디스크용 CSI 드라이버는 볼륨 복제를 지원 합니다. 시연 하려면 [이전에 만든](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) 의 [복제 된 볼륨과](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) `azuredisk-pvc` 이를 [사용 하는 새 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)를 만듭니다.



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

이제 아래를 실행 하 여 복제 된 볼륨의 콘텐츠를 확인 하 고 여전히 생성 된 파일을 확인할 수 있습니다 `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>영구적 볼륨 크기 조정 (PV)

대신 더 큰 용량의 PVC를 요청할 수 있습니다. PVC 개체를 편집 하 고 더 큰 크기를 지정 합니다. 이렇게 변경 하면 PersistentVolume를 지 원하는 기본 볼륨의 확장이 트리거됩니다. 

> [!NOTE] 
> 새 PersistentVolume는 클레임을 충족 하기 위해 생성 되지 않습니다. 대신 기존 볼륨의 크기가 조정 됩니다.

AKS에서 기본 제공 `managed-csi` 저장소 클래스는 이미 확장을 허용 하므로 [이 저장소 클래스를 사용 하 여 이전에 만든 PVC](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)를 활용 합니다. PVC가 10Gi 영구 볼륨을 요청 했습니다. 다음을 실행 하 여 확인할 수 있습니다.

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> 현재 Azure disk CSI 드라이버는 pod 연결 되지 않은 (그리고 특정 노드에 탑재 되지 않은 볼륨) 크기 조정 Pvc만 지원 합니다.

따라서 이전에 만든 pod를 삭제할 수 있습니다.

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

필드를 늘려서 PVC를 확장 해 보겠습니다 `spec.resources.requests.storage` .

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

이제 볼륨이 더 큰지 확인해 보겠습니다.

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> PVC가 다시 연결 될 때까지 새 크기를 반영 하지 않습니다.

새 pod를 만들어 보겠습니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

그리고 마지막으로 PVC의 크기를 확인 하 고 pod 내에서: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Windows 컨테이너

또한 Azure disk CSI 드라이버는 Windows 노드와 컨테이너를 지원 합니다. Windows 컨테이너를 사용 하려는 경우 windows [컨테이너 자습서](windows-container-cli.md) 에 따라 windows 노드 풀을 추가 합니다.

Windows 노드 풀을 만든 후에는와 같은 기본 제공 저장소 클래스를 활용할 수 있습니다 `managed-csi` . Kubectl apply 명령을 사용 하 여 아래를 배포 하 여 타임 스탬프를 파일에 저장 하는 예제 [windows 기반 상태 저장 집합](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 을 배포할 수 있습니다 `data.txt` . [kubectl apply][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

이제 다음을 실행 하 여 볼륨 내용의 유효성을 검사할 수 있습니다.

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>다음 단계

- Azure 파일용 CSI driver를 사용 하는 방법을 알아보려면 [CSI 드라이버에서 azure Files 사용](azure-files-csi.md)을 참조 하세요.
- 저장소 모범 사례에 대 한 자세한 내용은 [Azure Kubernetes Service의 저장소 및 백업 모범 사례 (AKS)][operator-best-practices-storage] 를 참조 하세요.


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register