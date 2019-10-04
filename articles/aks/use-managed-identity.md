---
title: Azure Kubernetes Service에서 관리 되는 id 사용
description: Azure Kubernetes 서비스 (AKS)에서 관리 id를 사용 하는 방법 알아보기
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827537"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>미리 보기-Azure Kubernetes Service에서 관리 되는 id 사용

현재 사용자는 AKS 클러스터 (특히 Kubernetes 클라우드 공급자)가 Azure에서 부하 분산 장치 및 관리 디스크와 같은 추가 리소스를 만들기 위해 서비스 주체를 제공 하거나 사용자를 대신 하 여 AKS를 만듭니다. 서비스 사용자는 일반적으로 만료 날짜를 사용 하 여 생성 됩니다. 결과적으로 클러스터는 서비스 주체를 갱신 해야 하는 상태에 도달 하며, 그렇지 않으면 클러스터가 작동 하지 않습니다. 서비스 주체를 관리 하면 복잡성이 증가 합니다. 관리 Id는 기본적으로 서비스 사용자를 중심으로 하는 래퍼로, 관리를 단순화 합니다. [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 문서를 참조 하세요.

AKS는 시스템 할당 관리 id와 사용자 할당 id의 두 가지 관리 id를 만듭니다. 시스템 할당 관리 id는 kubernetes 클라우드 공급자가 사용자를 대신 하 여 Azure 리소스를 만드는 데 사용 됩니다. 이 시스템 할당 관리 id의 수명 주기는 클러스터와 연결 되며 클러스터를 삭제 하면 삭제 됩니다. 또한 AKS는 AKS가 Acr에 액세스할 수 있는 권한을 부여 하기 위해 클러스터에서 사용 되는 사용자 할당 관리 id와 Azure에서 메타 데이터를 가져오는 kubelet을 만듭니다.

이 미리 보기 기간에는 서비스 사용자가 여전히 필요 합니다. 모니터링, 가상 노드, azure 정책 및 http 응용 프로그램 라우팅과 같은 추가 기능을 권한 부여 하는 데 사용 됩니다. SPN에서 addons의 종속성을 제거 하는 작업이 진행 중 이며 궁극적으로 AKS의 SPN 요구 사항은 완전히 제거 됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전 주의 사항

다음이 있어야 합니다.

* Azure CLI 버전 2.0.70 이상 및 aks-preview 0.4.14 확장도 필요 합니다.

## <a name="install-latest-aks-cli-preview-extension"></a>최신 AKS CLI 미리 보기 확장 설치

**Aks-preview 0.4.14** extension 이상이 필요 합니다.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

상태를 *등록 된*것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. [Az feature list] [az-기능 목록] 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

상태가 등록 됨 인 경우 [az provider register] [az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>관리 id를 사용 하 여 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용 하 여 관리 id를 사용 하 여 AKS 클러스터를 만들 수 있습니다.
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>클러스터에 액세스 하기 위한 자격 증명 가져오기
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
몇 분 후에 클러스터가 만들어지면 응용 프로그램 워크 로드를 배포 하 고 서비스 주체 기반 AKS 클러스터를 사용 하는 것과 동일한 방식으로 조작할 수 있습니다. 

> [!IMPORTANT]
> * 관리 id를 사용 하는 AKS 클러스터는 클러스터를 만드는 동안에만 사용할 수 있습니다.
> * 관리 되는 id를 사용 하도록 기존 AKS 클러스터를 업데이트/업그레이드할 수 없음