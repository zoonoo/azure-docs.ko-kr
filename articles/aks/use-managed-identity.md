---
title: Azure Kubernetes Service에서 관리 되는 id 사용
description: Azure Kubernetes 서비스 (AKS)에서 관리 id를 사용 하는 방법 알아보기
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112969"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 관리 되는 id 사용

현재 azure Kubernetes 서비스 (AKS) 클러스터 (특히, Kubernetes 클라우드 공급자)는 id를 사용 하 여 Azure에서 부하 분산 장치 및 관리 디스크와 같은 추가 리소스를 만들어야 합니다 .이 id는 *관리 id* 또는 *서비스 사용자*일 수 있습니다. [서비스 주체](kubernetes-service-principal.md)를 사용 하는 경우 하나를 제공 하거나 사용자를 대신 하 여 AKS를 만들어야 합니다. 관리 id를 사용 하는 경우 자동으로 AKS에 의해 생성 됩니다. 서비스 주체를 사용 하는 클러스터는 궁극적으로 클러스터 작동을 유지 하기 위해 서비스 주체를 갱신 해야 하는 상태에 도달 합니다. 서비스 주체를 관리 하면 복잡성이 증가 하므로 관리 되는 id를 대신 사용 하는 것이 더 쉽습니다. 서비스 사용자와 관리 되는 id 모두에 대해 동일한 권한 요구 사항이 적용 됩니다.

*관리 id* 는 기본적으로 서비스 사용자를 중심으로 하는 래퍼로, 관리를 단순화 합니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

AKS은 두 개의 관리 되는 id를 만듭니다.

- **시스템 할당 관리 id**: Kubernetes 클라우드 공급자가 사용자를 대신 하 여 Azure 리소스를 만드는 데 사용 하는 id입니다. 시스템 할당 id의 수명 주기는 클러스터와 연결 됩니다. 이 id는 클러스터가 삭제 될 때 삭제 됩니다.
- **사용자 할당 관리 id**: 클러스터에서 권한 부여에 사용 되는 id입니다. 예를 들어 사용자 할당 id를 사용 하 여 AKS에서 Azure Container registry (Acr)를 사용 하거나 Azure에서 메타 데이터를 가져오도록 kubelet에 권한을 부여 합니다.

또한 추가 기능은 관리 되는 id를 사용 하 여 인증 합니다. 각 추가 기능에 대해 관리 id는 AKS에 의해 생성 되 고 추가 기능에 대해 지속 됩니다. 

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스를 설치 해야 합니다.

- Azure CLI 버전 2.2.0 이상

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리 id를 사용 하 여 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용 하 여 관리 id를 사용 하 여 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

그런 다음 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

관리 id를 사용 하 여 성공적으로 클러스터를 만들려면 다음 서비스 사용자 프로필 정보가 포함 됩니다.

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> 리소스가 MC_ * 리소스 그룹의 외부에 있는 경우 고유한 VNet, 고정 IP 주소 또는 연결 된 Azure 디스크를 만들고 사용 하려면 클러스터 시스템 할당 관리 Id의 PrincipalID를 사용 하 여 역할 할당을 수행 합니다. 역할 할당에 대 한 자세한 내용은 [다른 Azure 리소스에 대 한 액세스 위임](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)을 참조 하세요.
>
> Azure Cloud provider에서 사용 하는 클러스터 관리 Id에 대 한 권한 부여를 채우는 데 60 분 정도 걸릴 수 있습니다.

마지막으로 클러스터에 액세스 하기 위한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

클러스터가 몇 분 안에 생성 됩니다. 그런 다음 응용 프로그램 워크 로드를 새 클러스터에 배포 하 고 서비스 주체 기반 AKS 클러스터에서 수행한 것 처럼 상호 작용할 수 있습니다.

> [!IMPORTANT]
>
> - 관리 id를 사용 하는 AKS 클러스터는 클러스터를 만드는 동안에만 사용할 수 있습니다.
> - 관리 되는 id를 사용 하도록 기존 AKS 클러스터를 업데이트 하거나 업그레이드할 수 없습니다.
