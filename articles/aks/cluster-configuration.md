---
title: Azure Kubernetes 서비스(AKS)의 클러스터 구성
description: AZURE Kubernetes 서비스(AKS)에서 클러스터를 구성하는 방법 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479164"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정의 일환으로 필요에 맞게 클러스터 구성을 사용자 지정해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정하기 위한 몇 가지 옵션을 소개합니다.

## <a name="os-configuration-preview"></a>OS 구성(미리 보기)

AKS는 이제 프리뷰에서 노드 운영 체제(OS)로 우분투 18.04를 지원합니다. 미리 보기 기간 동안, 우분투 16.04 와 우분투 18.04 사용할 수 있습니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI, 버전 2.2.0 이상
- aks 미리 보기 0.4.35 확장

aks 미리 보기 0.4.35 이상 확장을 설치하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

피처 `UseCustomizedUbuntuPreview` 등록:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨으로**표시되는 데 몇 분 정도 걸릴 수 있습니다. [az 기능 목록](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

상태가 등록된 것으로 표시되면 az 공급자 `Microsoft.ContainerService` 레지스터 명령을 사용하여 리소스 [공급자의](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 등록을 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

클러스터가 생성될 때 우분투 18.04를 사용하도록 클러스터를 구성합니다. 플래그를 `--aks-custom-headers` 사용하여 우분투 18.04를 기본 OS로 설정합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

일반 우분투를 만들려면 16.04 클러스터, 사용자 지정 `--aks-custom-headers` 태그를 생략 하 여 그렇게 할 수 있습니다.

## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에 Azure Kubernetes 서비스 클러스터를 배포하면 작업자 노드에 대해 두 번째 리소스 그룹이 만들어집니다. 기본적으로 AKS는 노드 리소스 그룹의 `MC_resourcegroupname_clustername_location`이름을 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치합니다. Azure CLI를 사용하여 `--node-resource-group` 명령의 `az aks create` 매개 변수를 사용하여 리소스 그룹에 대한 사용자 지정 이름을 지정합니다. Azure 리소스 관리자 템플릿을 사용하여 AKS 클러스터를 배포하는 경우 `nodeResourceGroup` 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

보조 리소스 그룹은 자체 구독의 Azure 리소스 공급자에 의해 자동으로 만들어집니다. 클러스터를 만들 때 사용자 지정 리소스 그룹 이름만 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때 다음을 수행할 수 없습니다.

- 노드 리소스 그룹에 대한 기존 리소스 그룹을 지정합니다.
- 노드 리소스 그룹에 대해 다른 구독을 지정합니다.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
- 노드 리소스 그룹 내에서 관리되는 리소스에 대한 이름을 지정합니다.
- 노드 리소스 그룹 내에서 관리되는 리소스의 Azure 생성 태그를 수정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 클러스터의 Linux `Kured` [노드에 보안 및 커널 업데이트를 적용하는](node-updates-kured.md) 데 사용하는 방법에 대해 알아봅니다.
- [Azure Kubernetes 서비스(AKS) 클러스터 업그레이드를](upgrade-cluster.md) 참조하여 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아봅니다.
- [AKS에 대한 자주 묻는 질문](faq.md) 목록을 참조하여 일반적인 AKS 질문에 대한 답변을 찾아보십시오.