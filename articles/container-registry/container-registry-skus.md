---
title: Azure Container Registry SKU
description: "Azure Container Registry에서 사용 가능한 다양한 서비스 계층 간 비교"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: dae97084bdaab77efd38169cdf7e70c827b0b5ab
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

ACR(Azure Container Registry)은 다중 서비스 계층(즉, SKU)에서 사용 가능합니다. 이러한 SKU는 예측 가능한 가격 책정과 Azure에서 개인 Docker 레지스트리를 사용하는 방법에 대한 여러 옵션을 제공합니다. 더 높은 수준의 SKU를 선택하면 향상된 성능과 규모를 제공합니다. 그러나 모든 SKU는 개발자가 Basic으로 시작해서 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 전환할 수 있도록 동일한 프로그래밍 성능을 제공합니다.

## <a name="basic"></a>Basic
개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. Basic 레지스트리는 Standard 및 Premium(Azure Active Directory 인증 통합, 이미지 삭제 및 웹 후크)과 동일한 프로그래밍 성능을 가지고 있지만 크기와 사용량 제약이 있습니다.

## <a name="standard"></a>Standard
Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 저장소 제한과 이미지 처리량이 증가합니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다.

## <a name="premium"></a>Premium
Premium 레지스트리는 저장소 및 동시 작업 수 등의 제약에 더 높은 한도를 제공하여 대규모 시나리오가 가능합니다. Premium은 더 높은 이미지 처리량뿐만 아니라, 각 배포에 네트워크에 가까운 레지스트리를 유지하면서 여러 지역 전반에서 단일 레지스트리를 관리하기 위해 [지역에서 복제](container-registry-geo-replication.md)와 같은 기능이 추가됩니다.

## <a name="classic"></a>클래식
Classic 레지스트리 SKU는 Azure에서 Azure Container Registry 서비스의 초기 릴리스를 지원했습니다. Classic 레지스트리는 사용자 구독에서 Azure가 만드는 저장소 계정에서 지원하며 ACR가 처리량 증가 및 지역에서 복제 등의 더 높은 수준의 기능을 제공할 수 있는 기능을 제한합니다. 제한된 기능으로 인해 앞으로 Classic SKU를 폐기할 계획입니다.

> [!NOTE]
> Classic 레지스트리 SKU 폐기가 계획되어 있으므로, 모든 새로운 레지스트리에는 Basic, Standard 또는 Premium을 사용하는 것이 좋습니다. 기존 Classic 레지스트리 전환 방법에 대한 자세한 내용은 [SKU 변경](#changing-skus)을 참조하세요.
>

## <a name="registry-sku-feature-matrix"></a>레지스트리 SKU 기능 행렬

다음 표에는 Basic, Standard 및 Premium 서비스 계층의 기능 및 한계에 대해 자세히 나와 있습니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>레지스트리 크기 관리
각 SKU의 저장소 제약 조건은 일반적인 시나리오(시작은 Basic, 다수의 프로덕션 앱은 Standard, 초대형 성능과 [지역에서 복제](container-registry-geo-replication.md)는 Premium)와 맞추기 위한 것입니다. 레지스트리 수명 동안에 미사용 콘텐츠는 정기적으로 삭제하여 크기를 관리해야 합니다.

Azure Portal의 컨테이너 레지스트리 **개요**에서 레지스트리의 현재 사용량을 확인할 수 있습니다.

![Azure Portal의 레지스트리 사용량 정보](media/container-registry-skus/registry-overview-quotas.png)

Azure Portal에서 리포지터리를 삭제하여 레지스트리의 크기를 관리할 수 있습니다.

**서비스**에서 **리포지토리**를 선택한 다음, 삭제하려는 리포지토리를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

![Azure Portal에서 리포지토리 삭제](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>SKU 변경

Azure Portal에서 레지스트리의 SKU를 변경할 수 있습니다.

Azure Portal의 레지스트리 **개요**에서 **업데이트**를 선택한 다음, SKU 드롭다운에서 새로운 **SKU**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Classic에서 변경
Classic 레지스트리를 Basic, Standard 또는 Premium으로 변경 시 Azure는 기존 컨테이너 이미지를 구독에서 연결된 저장소 계정에서 Azure에서 관리하는 저장소 계정으로 복사합니다. 이 프로세스는 시간이 다소 걸릴 수 있습니다.

전환 중에 `docker pull`은 계속 작동하지만, `docker push`는 전환이 완료될 때까지 차단됩니다.

완료되면 해당 구독 저장소 계정은 더 이상 ACR에서 사용되지 않습니다.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Classic에서 Basic, Standard 또는 Premium으로 변경하는 이유는?

Classic 레지스트리의 제한된 기능으로 인해 Classic 레지스트리를 Basic, Standard 또는 Premium 계층으로 변경하는 것이 좋습니다. 이러한 더 높은 수준의 SKU는 레지스트리를 Azure 기능에 더 깊이 통합합니다. 이러한 기능은 다음과 같습니다.

* 개별 인증을 위한 Azure Active Directory 통합([az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) 참조).
* 이미지 및 태그 삭제 지원
* [지역에서 복제](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

무엇보다도 Classic 레지스트리는 해당 레지스트리를 만들 때 Azure 구독에서 Azure가 자동으로 프로비전한 저장소 계정에 따라 결정됩니다. 반면, Basic, Standard 및 Premium SKU는 *관리되는 저장소*를 활용합니다. 즉, Azure는 사용자를 위해 이미지의 저장소를 투명하게 관리하여 별도의 저장소 계정이 자체 구독에서 만들어지지 않습니다.

Basic, Standard 및 Premium에서 제공하는 관리되는 저장소에는 다음과 같은 이점이 일부 있습니다.

* 컨테이너 이미지는 [암호화된 상태로 유지](../storage/common/storage-service-encryption.md)됩니다.
* 이미지는 [지역 중복 저장소](../storage/common/storage-redundancy.md#geo-redundant-storage)를 사용하여 저장되므로 여러 지역 복제 시 이미지를 확실하게 백업할 수 있습니다.
* [SKU 간 이동](#changing-skus) 기능으로 더 높은 수준의 SKU를 선택하면 더 높은 처리량을 지원할 수 있습니다. 각 SKU를 사용하여 ACR은 요구 사항 증가에 따라 처리량 요구 사항을 충족할 수 있습니다. ACR에서 원하는 처리량을 달성하는 방식에 대한 기본 구현은 구현 세부 사항을 관리하지 않아도 더 높은 SKU를 선택하면 *기본*으로 표현됩니다.

## <a name="pricing"></a>가격

각 Azure Container Registry SKU에 대한 가격 책정 정보는 [컨테이너 레지스트리 가격 책정](https://azure.microsoft.com/pricing/details/container-registry/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

**Azure Container Registry 로드맵**

GitHub에서 [ACR 로드맵](https://aka.ms/acr/roadmap)에 방문하여 예정된 서비스 기능에 대한 정보를 알아 보세요.

**Azure Container Registry UserVoice**

[ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry)에서 새로운 기능 제안 사항을 제출하고 투표합니다.