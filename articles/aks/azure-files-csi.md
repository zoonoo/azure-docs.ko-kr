---
title: AKS (Azure Kubernetes Service)의 Azure Files에 대 한 CSI (Container Storage Interface) 드라이버 사용
description: AKS (Azure Kubernetes Service) 클러스터에서 Azure Files에 CSI (Container Storage Interface) 드라이버를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 330c1b74a46b0f18af1068797d080e903f516ea6
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089873"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service) (미리 보기)에서 CSI (Azure Files Container Storage Interface) 드라이버 사용

CSI (Azure Files Container Storage Interface) 드라이버는 AKS (Azure Kubernetes Service)에서 Azure Files 공유의 수명 주기를 관리 하는 데 사용 하는 [CSI 사양](https://github.com/container-storage-interface/spec/blob/master/spec.md)규격 드라이버입니다.

CSI은 임의 블록 및 파일 저장소 시스템을 Kubernetes의 컨테이너 화 된 워크 로드에 노출 하는 표준입니다. AKS는 CSI을 채택 하 고 사용 하 여 코어 Kubernetes 코드를 터치 하 고 해당 릴리스 주기를 기다릴 필요 없이 Kubernetes의 기존 저장소 시스템을 새로 제공 하거나 향상 시킬 수 있도록 플러그 인을 작성, 배포 및 반복할 수 있습니다.

CSI driver를 지 원하는 AKS 클러스터를 만들려면 [Azure 디스크에 대 한 CSI 드라이버 사용 및 AKS에서 Azure Files](csi-storage-drivers.md)을 참조 하세요.

>[!NOTE]
> *트리 내 드라이버* 는 핵심 Kubernetes 코드의 일부인 현재 저장소 드라이버와 플러그 인 새 CSI 드라이버를 나타냅니다.

## <a name="use-a-persistent-volume-with-azure-files"></a>Azure Files에서 영구적 볼륨 사용

[영구적 볼륨 (PV)](concepts-storage.md#persistent-volumes) 은 Kubernetes pod와 함께 사용 하기 위해 프로 비전 된 저장소 부분을 나타냅니다. PV는 하나 이상의 pod에서 사용할 수 있으며 동적 또는 정적으로 프로 비전 할 수 있습니다. 여러 pod가 동일한 저장소 볼륨에 동시에 액세스 해야 하는 경우 Azure Files를 사용 하 여 SMB ( [서버 메시지 블록) 프로토콜][smb-overview]을 사용 하 여 연결할 수 있습니다. 이 문서에서는 AKS 클러스터의 여러 pod에서 사용할 Azure Files 공유를 동적으로 만드는 방법을 보여 줍니다. 정적 프로 비전의 경우 [Azure Files 공유를 사용 하 여 볼륨 수동 만들기 및 사용](azure-files-volume.md)을 참조 하세요.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>기본 제공 저장소 클래스를 사용 하 여 동적으로 Azure Files PVs 만들기

저장소 클래스는 Azure Files 공유를 만드는 방법을 정의 하는 데 사용 됩니다. 저장소 계정은 Azure Files 공유를 저장 하기 위해 저장소 클래스와 함께 사용 하기 위해 [노드 리소스 그룹][node-resource-group] 에 자동으로 만들어집니다. 다음 [Azure storage 중복 sku][storage-skus] *에 대해 다음 중 하나를 선택*합니다.

* **Standard_LRS**: 표준 로컬 중복 저장소
* **Standard_GRS**: 표준 지역 중복 저장소
* **Standard_ZRS**: 표준 영역 중복 저장소
* **Standard_RAGRS**: 표준 읽기 액세스 지역 중복 저장소
* **Premium_LRS**: 프리미엄 로컬 중복 저장소

> [!NOTE]
> Azure Files는 Azure Premium Storage를 지원 합니다. 최소 프리미엄 파일 공유는 100 GB입니다.

AKS에서 storage CSI 드라이버를 사용 하 `StorageClasses` 는 경우 AZURE FILES CSI storage 드라이버를 사용 하는 두 가지 추가 기본 제공이 있습니다. 추가 CSI 저장소 클래스는 클러스터에서 트리 내 기본 저장소 클래스와 함께 생성 됩니다.

- `azurefile-csi`: Azure Standard Storage를 사용 하 여 Azure Files 공유를 만듭니다.
- `azurefile-csi-premium`: Azure Premium Storage를 사용 하 여 Azure Files 공유를 만듭니다.

두 저장소 클래스의 회수 정책은 해당 PV를 삭제할 때 기본 Azure Files 공유가 삭제 되도록 합니다. 저장소 클래스는 또한 확장 가능 하도록 파일 공유를 구성 하므로 새 크기의 PVC (영구적 볼륨 클레임)만 편집 하면 됩니다.

이러한 저장소 클래스를 사용 하려면 해당 클래스를 참조 하 고 사용 하는 [PVC](concepts-storage.md#persistent-volume-claims) 및 해당 pod를 만듭니다. PVC는 저장소 클래스를 기반으로 저장소를 자동으로 프로 비전 하는 데 사용 됩니다. PVC는 미리 만든 저장소 클래스 또는 사용자 정의 저장소 클래스 중 하나를 사용 하 여 원하는 SKU 및 크기에 대 한 Azure Files 공유를 만들 수 있습니다. Pod 정의를 만들 때 필요한 저장소를 요청 하도록 PVC가 지정 됩니다.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 [현재 날짜를 `outfile` 에 출력 하는 예제 PVC 및 pod](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) 를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Pod가 실행 중 상태가 된 후에는 다음 명령을 실행 하 고 출력에이 포함 되어 있는지 확인 하 여 파일 공유가 올바르게 탑재 되었는지 확인할 수 있습니다 `outfile` .

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>사용자 지정 저장소 클래스 만들기

기본 저장소 클래스는 가장 일반적인 시나리오에 적합 하지만 모든 것은 아닙니다. 경우에 따라 고유한 매개 변수를 사용 하 여 고유한 저장소 클래스를 사용자 지정 해야 할 수 있습니다. 예를 들어 다음 매니페스트를 사용 하 여 `mountOptions` 파일 공유의를 구성 합니다.

*FileMode* 및 *dimode* 의 기본값은 Kubernetes 탑재 된 파일 공유의 경우 *0777* 입니다. 저장소 클래스 개체에 다른 탑재 옵션을 지정할 수 있습니다.

이라는 파일을 만들고 `azure-file-sc.yaml` 다음 예제 매니페스트를 붙여넣습니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만듭니다.

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure Files CSI 드라이버는 영구적 볼륨 및 기본 파일 공유 [의 스냅숏을](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) 만드는 것을 지원 합니다.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 [볼륨 스냅숏 클래스](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) 를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

[이 자습서의 시작 부분에서 동적으로 만든](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)PVC에서 [볼륨 스냅숏을](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) 만듭니다 `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

스냅숏이 올바르게 만들어졌는지 확인 합니다.

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>영구적 볼륨 크기 조정

PVC에 대해 더 큰 볼륨을 요청할 수 있습니다. PVC 개체를 편집 하 고 더 큰 크기를 지정 합니다. 이 변경으로 인해 PV를 지 원하는 기본 볼륨의 확장이 트리거됩니다.

> [!NOTE]
> 새 PV는 클레임을 충족 하기 위해 생성 되지 않습니다. 대신 기존 볼륨의 크기가 조정 됩니다.

AKS에서 기본 제공 `azurefile-csi` 저장소 클래스는 이미 확장을 지원 하므로 [이 저장소 클래스를 사용 하 여 이전에 만든 PVC](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)를 사용 합니다. PVC가 100Gi 파일 공유를 요청 했습니다. 다음을 실행 하 여 확인할 수 있습니다.

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

필드를 늘려서 PVC를 확장 합니다 `spec.resources.requests.storage` .

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Pod 내의 PVC와 파일 시스템이 모두 새 크기를 표시 하는지 확인 합니다.

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Windows 컨테이너

Azure Files CSI 드라이버는 Windows 노드와 컨테이너도 지원 합니다. Windows 컨테이너를 사용 하려는 경우 windows [컨테이너 자습서](windows-container-cli.md) 에 따라 windows 노드 풀을 추가 합니다.

Windows 노드 풀을 만든 후와 같은 기본 제공 저장소 클래스를 사용 `azurefile-csi` 하거나 사용자 지정 저장소 클래스를 만듭니다. Kubectl apply 명령을 사용 하 여 다음 명령을 배포 하 여 타임 스탬프를 파일에 저장 하는 예제 [Windows 기반 상태 저장 집합](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 을 배포할 수 있습니다 `data.txt` . [kubectl apply][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

다음을 실행 하 여 볼륨 콘텐츠의 유효성을 검사 합니다.

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>다음 단계

- Azure 디스크에 CSI 드라이버를 사용 하는 방법을 알아보려면 [CSI 드라이버에서 Azure 디스크 사용](azure-disk-csi.md)을 참조 하세요.
- 저장소 모범 사례에 대 한 자세한 내용은 [Azure Kubernetes Service의 저장소 및 백업 모범 사례][operator-best-practices-storage]를 참조 하세요.


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md