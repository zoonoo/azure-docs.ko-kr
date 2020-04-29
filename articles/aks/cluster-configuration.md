---
title: AKS (Azure Kubernetes Services)의 클러스터 구성
description: Azure Kubernetes 서비스 (AKS)에서 클러스터를 구성 하는 방법을 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479164"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정에서 필요에 맞게 클러스터 구성을 사용자 지정 해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정 하기 위한 몇 가지 옵션을 소개 합니다.

## <a name="os-configuration-preview"></a>OS 구성 (미리 보기)

이제 AKS는 미리 보기에서 노드 OS (운영 체제)로 Ubuntu 18.04을 지원 합니다. Preview 기간 동안 Ubuntu 16.04 및 Ubuntu 18.04을 모두 사용할 수 있습니다.

다음 리소스를 설치 해야 합니다.

- Azure CLI 버전 2.2.0 이상
- Aks-preview 0.4.35 확장

Aks-preview 0.4.35 extension 이상을 설치 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

기능을 `UseCustomizedUbuntuPreview` 등록 합니다.

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

상태를 **등록 된**것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. [Az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

상태가 등록 됨으로 표시 되 면 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용 `Microsoft.ContainerService` 하 여 리소스 공급자 등록을 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

클러스터를 만들 때 Ubuntu 18.04를 사용 하도록 클러스터를 구성 합니다. 플래그를 `--aks-custom-headers` 사용 하 여 Ubuntu 18.04을 기본 OS로 설정 합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

일반 Ubuntu 16.04 클러스터를 만들려는 경우 사용자 지정 `--aks-custom-headers` 태그를 생략 하 여 수행할 수 있습니다.

## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에서 Azure Kubernetes 서비스 클러스터를 배포 하는 경우 작업자 노드에 대 한 두 번째 리소스 그룹이 생성 됩니다. 기본적으로 AKS는 노드 리소스 그룹 `MC_resourcegroupname_clustername_location`의 이름을 지정 하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정 하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치 합니다. Azure CLI 사용 하 여 `--node-resource-group` `az aks create` 명령의 매개 변수를 사용 하 여 리소스 그룹에 대 한 사용자 지정 이름을 지정 합니다. Azure Resource Manager 템플릿을 사용 하 여 AKS 클러스터를 배포 하는 경우 `nodeResourceGroup` 속성을 사용 하 여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

사용자의 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다. 클러스터를 만들 때에만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때 다음을 수행할 수 없다는 점에 유의 하세요.

- 노드 리소스 그룹에 대 한 기존 리소스 그룹을 지정 합니다.
- 노드 리소스 그룹에 대해 다른 구독을 지정 하십시오.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경 합니다.
- 노드 리소스 그룹 내에서 관리 되는 리소스의 이름을 지정 합니다.
- 노드 리소스 그룹 내에서 관리 되는 리소스의 Azure에서 만든 태그를 수정 하거나 삭제 합니다.

## <a name="next-steps"></a>다음 단계

- 를 사용 `Kured` 하 여 클러스터의 [Linux 노드에 보안 및 커널 업데이트를 적용](node-updates-kured.md) 하는 방법을 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드 하는 방법을 알아보려면 [Azure Kubernetes 서비스 (AKS) 클러스터 업그레이드](upgrade-cluster.md) 를 참조 하세요.
- 몇 가지 일반적인 AKS 질문에 대 한 답변을 찾기 위해 [AKS에 대 한 자주 묻는 질문](faq.md) 의 목록을 참조 하세요.