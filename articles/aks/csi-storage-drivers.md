---
title: AKS (Azure Kubernetes Service)에서 CSI (Container Storage Interface) 드라이버를 사용 하도록 설정
description: AKS (Azure Kubernetes Service) 클러스터에서 Azure 디스크 및 Azure Files에 대 한 CSI (Container Storage Interface) 드라이버를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2af4f9e2ea1dc0fcb8e5f40e0024297124292b49
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074824"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service) (미리 보기)에서 Azure 디스크 및 Azure Files에 대 한 CSI (Container Storage Interface) 드라이버를 사용 하도록 설정

CSI (Container Storage Interface)는 Kubernetes에서 임의 블록 및 파일 저장소 시스템을 컨테이너 화 된 작업에 노출 하는 표준입니다. AKS (Azure Kubernetes Service)는 CSI을 채택 하 고 사용 하 여 코어 Kubernetes 코드를 터치 하 고 해당 릴리스 주기를 기다릴 필요 없이 Kubernetes에서 새로운 기존 저장소 시스템을 제공 하거나 새로운 기능을 제공 하기 위해 플러그 인을 작성, 배포 및 반복할 수 있습니다.

AKS의 CSI storage driver support를 사용 하면 기본적으로 다음을 사용할 수 있습니다.
- [*Azure 디스크*](azure-disk-csi.md)는 Kubernetes *datadisk* 리소스를 만드는 데 사용할 수 있습니다. 디스크는 고성능 Ssd에서 지원 되는 Azure Premium Storage 또는 일반 Hdd 또는 표준 Ssd에서 지 원하는 Azure Standard Storage를 사용할 수 있습니다. 대부분의 프로덕션 및 개발 워크 로드의 경우 Premium Storage를 사용 합니다. Azure 디스크는 *Readwriteonce 번*탑재 되므로 단일 pod 에서만 사용할 수 있습니다. 여러 pod에서 동시에 액세스할 수 있는 저장소 볼륨의 경우 Azure Files를 사용 합니다.
- [*Azure Files*](azure-files-csi.md)는 Azure Storage 계정에서 지원 되는 SMB 3.0 공유를 pod에 탑재 하는 데 사용할 수 있습니다. Azure Files를 사용 하 여 여러 노드와 pod 간에 데이터를 공유할 수 있습니다. Azure Files 일반 Hdd에서 지 원하는 Azure Standard Storage 또는 고성능 Ssd에서 지원 되는 Azure Premium Storage를 사용할 수 있습니다.

> [!IMPORTANT]
> Kubernetes 버전 1.21부터 Kubernetes는 기본적으로 CSI 드라이버만 사용 합니다. 이러한 드라이버는 Kubernetes에서 저장소 지원의 미래입니다.
>
> *트리 내 드라이버* 는 핵심 Kubernetes 코드의 일부인 현재 저장소 드라이버와 플러그 인 새 CSI 드라이버를 나타냅니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 클러스터를 만들 때에만 설정할 수 있습니다.
- CSI 드라이버를 지 원하는 최소 Kubernetes 부 버전은 v 1.17입니다.
- 미리 보기 중에는 기본 저장소 클래스가 여전히 [동일한 트리 내 저장소 클래스](concepts-storage.md#storage-classes)입니다. 이 기능을 일반적으로 사용할 수 있게 되 면 기본 저장소 클래스가 `managed-csi` 저장소 클래스가 되 고 트리 내 저장소 클래스가 제거 됩니다.
- 첫 번째 미리 보기 단계에서는 Azure CLI만 지원 됩니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>`EnableAzureDiskFileCSIDriver`미리 보기 기능 등록

Azure 디스크 및 Azure Files에 대 한 CSI 드라이버를 사용할 수 있는 AKS 클러스터를 만들려면 `EnableAzureDiskFileCSIDriver` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`EnableAzureDiskFileCSIDriver`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

CSI storage 드라이버를 사용할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 최신 *AKS-미리 보기* Azure CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>CSI 저장소 드라이버를 사용할 수 있는 새 클러스터 만들기

다음 CLI 명령을 사용 하 여 Azure 디스크에 CSI storage 드라이버를 사용 하 고 Azure Files 수 있는 새 클러스터를 만듭니다. 플래그를 사용 `--aks-custom-headers` 하 여 기능을 설정 `EnableAzureDiskFileCSIDriver` 합니다.

Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

CSI 저장소 드라이버에 대 한 지원을 포함 하는 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

저장소 드라이버를 CSI 대신 트리 저장소 드라이버에서 클러스터를 만들려는 경우 사용자 지정 매개 변수를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` .


다음을 실행 하 여이 노드에 연결할 수 있는 Azure 디스크 기반 볼륨의 수를 확인 합니다.

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>다음 단계

- Azure 디스크에 CSI 드라이브를 사용 하려면 [CSI 드라이버에서 azure 디스크 사용](azure-disk-csi.md)을 참조 하세요.
- Azure Files에 대 한 CSI 드라이브를 사용 하려면 [CSI 드라이버와 함께 Azure Files 사용](azure-files-csi.md)을 참조 하세요.
- 저장소 모범 사례에 대 한 자세한 내용은 [Azure Kubernetes Service의 저장소 및 백업 모범 사례][operator-best-practices-storage]를 참조 하세요.

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
[premium-storage]: ../virtual-machines/disks-types.md
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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true