---
title: AKS(Azure Kubernetes Service)의 클러스터 구성
description: AKS(Azure Kubernetes Service)에서 클러스터를 구성하는 방법 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725149"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정에서 필요에 따라 클러스터 구성을 사용자 지정해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정하기 위한 몇 가지 옵션을 소개합니다.

## <a name="os-configuration-preview"></a>OS 구성(미리 보기)

이제 AKS는 미리 보기에서 노드 OS(운영 체제)로 Ubuntu 18.04를 지원합니다. 미리 보기 기간 동안 Ubuntu 16.04 및 Ubuntu 18.04를 모두 사용할 수 있습니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.2.0 이상
- aks-preview 0.4.35 확장

aks-preview 0.4.35 확장 이상을 설치하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

`UseCustomizedUbuntuPreview` 기능을 등록합니다.

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>새 클러스터

클러스터를 만들 때 Ubuntu 18.04를 사용하도록 클러스터를 구성합니다. `--aks-custom-headers` 플래그를 사용하여 Ubuntu 18.04를 기본 OS로 설정합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

일반 Ubuntu 16.04 클러스터를 만들려는 경우 사용자 지정 `--aks-custom-headers` 태그를 생략하여 수행할 수 있습니다.

### <a name="existing-clusters"></a>기존 클러스터

Ubuntu 18.04를 사용하도록 새 노드 풀을 구성합니다. `--aks-custom-headers` 플래그를 사용하여 Ubuntu 18.04를 해당 노드 풀의 기본 OS로 설정합니다.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

일반 Ubuntu 16.04 노드 풀을 만들려면 사용자 지정 `--aks-custom-headers` 태그를 생략하여 수행할 수 있습니다.


## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에서 Azure Kubernetes Service 클러스터를 배포하면 작업자 노드에 대한 두 번째 리소스 그룹이 생성됩니다. 기본적으로 AKS는 노드 리소스 그룹 이름을 `MC_resourcegroupname_clustername_location`으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치합니다. Azure CLI 사용하여 `az aks create` 명령의 `--node-resource-group` 매개 변수를 사용하여 리소스 그룹의 사용자 지정 이름을 지정합니다. Azure Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포하는 경우 `nodeResourceGroup` 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다. 클러스터가 생성될 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

- 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
- 노드 리소스 그룹에 다른 구독을 지정합니다.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 클러스터에서 `Kured`를 사용하여 [Linux 노드에 보안 및 커널 업데이트를 적용](node-updates-kured.md)하는 방법에 대해 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.