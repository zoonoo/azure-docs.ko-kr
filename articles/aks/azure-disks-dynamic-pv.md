---
title: "AKS에서 Azure 디스크 사용"
description: "AKS에서 Azure 디스크 사용"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: a4e4ce6a23f9f8a99d8ae5f9e4e2084e3b749017
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Azure 디스크가 포함된 영구적 볼륨 - 동적 프로비저닝

영구적 볼륨은 Kubernetes 클러스터에서 사용하도록 프로비전된 저장소 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 Azure 디스크를 AKS 클러스터에 있는 Kubernetes 영구적 볼륨으로 동적으로 프로비전하는 과정을 자세히 설명합니다. 

Kubernetes 영구적 볼륨에 대한 자세한 내용은 [Kubernetes 영구적 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="built-in-storage-classes"></a>기본 제공 저장소 클래스

저장소 클래스는 동적으로 생성되는 영구적 볼륨의 구성 방법을 정의하는 데 사용됩니다. Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조하세요.

모든 AKS 클러스터에는 Azure 디스크에 작동하도록 구성된 2개의 미리 만들어진 저장소 클래스가 포함되어 있습니다. `kubectl get storageclass` 명령을 사용하여 이러한 클래스를 볼 수 있습니다.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

저장소 클래스가 사용자 요구에 잘 맞으면 새로 만들 필요가 없습니다.

## <a name="create-storage-class"></a>저장소 클래스 만들기

Azure 디스크용으로 구성된 새 저장소 클래스를 만들려면 다음의 샘플 매니페스트를 사용하면 됩니다. 

`storageaccounttype` 값 `Standard_LRS`는 표준 디스크가 만들어짐을 나타냅니다. 이 값을 `Premium_LRS`로 변경하여 [프리미엄 디스크][premium-storage]를 만들 수 있습니다. 프리미엄 디스크를 사용하려면 AKS 노드 가상 머신 크기가 프리미엄 디스크와 호환되어야 합니다. 호환되는 크기 목록을 보려면 [이 문서][premium-storage]를 참조하세요.

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```



## <a name="create-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

영구적 볼륨 클레임은 저장소 클래스 개체를 사용하여 저장소 부분을 동적으로 프로비전합니다. Azure 디스크를 사용할 경우, AKS 리소스와 동일한 리소스 그룹에 디스크가 만들어집니다.

이 예제 매니페스트는 `azure-managed-disk` 저장소 클래스로 영구 볼륨 클레임을 만들어 크기가 `5GB`이고 `ReadWriteOnce` 액세스 권한을 갖는 디스크를 만듭니다. PVC 액세스 모드에 대한 자세한 내용은 [액세스 모드][access-modes]를 참조하세요.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 영구적 볼륨 클레임 `azure-managed-disk`을 사용하여 `/var/www/html` 경로에 Azure 디스크를 탑재하는 Pod를 만듭니다. 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>다음 단계

Azure 디스크를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[premium-storage]: ../virtual-machines/windows/premium-storage.md