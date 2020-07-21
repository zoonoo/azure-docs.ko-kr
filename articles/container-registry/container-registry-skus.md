---
title: 레지스트리 서비스 계층 및 기능
description: Azure Container Registry의 기본, 표준 및 프리미엄 서비스 계층(SKU)에 있는 기능 및 제한 사항을 알아봅니다.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 66cb5d7c3cdca45f7a44f0f23bfa449d5ade6dbf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512618"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry 서비스 계층

Azure Container Registry는 다중 서비스 계층(SKU라고도 함)에서 사용 가능합니다. 해당 계층은 예측 가능한 가격 책정과 Azure에서 프라이빗 Docker 레지스트리의 용량 및 사용량 패턴에 맞추기 위한 여러 옵션을 제공합니다.

| 계층 | Description |
| --- | ----------- |
| **기본** | 개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. 기본 레지스트리는 표준 및 프리미엄(예: Azure Active Directory [인증 통합](container-registry-authentication.md#individual-login-with-azure-ad), [이미지 삭제][container-registry-delete] 및 [웹후크][container-registry-webhook])과 동일한 프로그래밍 성능을 가집니다. 그렇지만 포함된 스토리지 및 이미지 처리량은 더 낮은 사용량 시나리오에 가장 적합합니다. |
| **Standard** | Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 포함된 스토리지 제한과 이미지 처리량은 더 높습니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다. |
| **Premium** | Premium 레지스트리는 포함된 스토리지 및 동시 작업 수가 가장 높으며 고용량 시나리오를 지원합니다. 프리미엄은 많은 이미지 처리량을 제공하는 것 외에도, 여러 지역에서 단일 레지스트리를 관리하기 위한 [지역 복제][container-registry-geo-replication], 이미지 태그 서명을 위한 [콘텐츠 신뢰](container-registry-content-trust.md), 레지스트리에 대한 액세스를 제한하기 위한 [프라이빗 엔드포인트를 사용한 프라이빗 링크](container-registry-private-link.md)와 같은 기능을 추가로 제공합니다. |

기본, 표준 및 프리미엄 계층은 모두 동일한 프로그래밍 성능을 제공합니다. 또한 Azure에서 완전히 관리하는 [이미지 스토리지][container-registry-storage]를 완벽하게 활용합니다. 더 높은 수준의 계층을 선택하면 향상된 성능과 규모를 제공합니다. 여러 서비스 계층을 사용하여 Basic로 시작한 다음, 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 변환할 수 있습니다.

## <a name="service-tier-features-and-limits"></a>서비스 계층 기능 및 한도

다음 표에서는 Basic, Standard 및 Premium 서비스 계층의 기능 및 레지스트리 제한에 대해 자세히 설명 합니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>계층 변경

Azure CLI를 사용하거나 Azure Portal에서 레지스트리의 서비스 계층을 변경할 수 있습니다. 전환하는 계층에 필요한 최대 스토리지 용량이 있는 한 계층 간에 자유롭게 이동할 수 있습니다. 

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 서비스 계층 간에 이동하려면 [az acr update][az-acr-update] 명령을 사용합니다. 예를 들어 Premium으로 전환하려면:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure Portal의 컨테이너 레지스트리 **개요**에서 **업데이트**를 선택한 다음, SKU 드롭다운에서 새로운 **SKU**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트][update-registry-sku]

## <a name="pricing"></a>가격 책정

각 Azure Container Registry 서비스 계층의 가격 책정 정보는 [Container Registry 가격 책정][container-registry-pricing]을 참조하세요.

데이터 전송 가격 책정에 대한 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

**Azure Container Registry 로드맵**

GitHub에서 [ACR 로드맵][acr-roadmap]에 방문하여 예정된 서비스 기능에 대한 정보를 알아 보세요.

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice]에서 새로운 기능 제안 사항을 제출하고 투표합니다.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
