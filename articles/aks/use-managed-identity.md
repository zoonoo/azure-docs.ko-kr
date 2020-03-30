---
title: Azure Kubernetes 서비스에서 관리되는 ID 사용
description: AZURE Kubernetes 서비스(AKS)에서 관리되는 ID를 사용하는 방법 알아보기
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369963"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes 서비스에서 관리되는 ID 사용

현재 AKS(Azure Kubernetes) 클러스터(특히 Kubernetes 클라우드 공급자)는 *서비스 주체가* Azure에서 로드 밸런서 및 관리되는 디스크와 같은 추가 리소스를 만들어야 합니다. 서비스 주체를 제공해야 하거나 AKS가 귀하를 대신하여 서비스 주체를 생성합니다. 서비스 주체는 일반적으로 만료 날짜가 있습니다. 클러스터는 결국 클러스터가 계속 작동하도록 서비스 주체를 갱신해야 하는 상태에 도달합니다. 서비스 주체를 관리하여 복잡성을 추가합니다.

*관리되는 ID는* 기본적으로 서비스 주체에 대한 래퍼이며 관리를 더 간단하게 만듭니다. 자세한 내용은 Azure [리소스에 대한 관리되는 ID에 대해](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)읽어보십시오.

AKS는 두 개의 관리되는 ID를 만듭니다.

- **시스템 할당 된 관리 되는 ID:** Kubernetes 클라우드 공급자사용자를 대신 Azure 리소스를 만드는 데 사용 하는 ID입니다. 시스템 할당된 ID의 수명 주기는 클러스터의 수명 주기와 연결됩니다. 클러스터가 삭제되면 ID가 삭제됩니다.
- **사용자 할당된 관리 ID**: 클러스터의 권한 부여에 사용되는 ID입니다. 예를 들어 사용자 할당ID는 AKS가 Azure 컨테이너 레지스트리(AC)를 사용하도록 권한을 부여하거나 kubelet이 Azure에서 메타데이터를 가져오는 권한을 부여할 때 사용됩니다.

또한 추가 기능도 관리되는 ID를 사용하여 인증합니다. 각 추가 기능의 경우 관리되는 ID는 AKS에 의해 만들어지고 추가 기능의 수명 동안 지속됩니다. 리소스가 MC_* 리소스 그룹 외부에 있는 고유한 VNet, 정적 IP 주소 또는 연결된 Azure 디스크를 만들고 사용하려면 클러스터의 PrincipalID를 사용하여 역할 할당을 수행합니다. 역할 할당에 대한 자세한 내용은 [다른 Azure 리소스에 대한 대리자 액세스를](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI, 버전 2.2.0 이상

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리되는 ID로 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용하여 관리되는 ID가 있는 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

그런 다음 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

관리 되는 ID를 사용 하 여 성공적인 클러스터 생성이 서비스 주체 프로필 정보를 포함 합니다.

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

마지막으로 클러스터에 액세스하는 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

클러스터는 몇 분 안에 만들어집니다. 그런 다음 응용 프로그램 워크로드를 새 클러스터에 배포하고 서비스 주체 기반 AKS 클러스터에서 수행한 것처럼 응용 프로그램 워크로드와 상호 작용할 수 있습니다.

> [!IMPORTANT]
>
> - 관리되는 ID가 있는 AKS 클러스터는 클러스터를 만드는 동안에만 사용할 수 있습니다.
> - 관리되는 ID를 사용하도록 설정하려면 기존 AKS 클러스터를 업데이트하거나 업그레이드할 수 없습니다.
