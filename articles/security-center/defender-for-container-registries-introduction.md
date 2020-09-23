---
title: 컨테이너 레지스트리 용 Azure Defender-혜택 및 기능
description: 컨테이너 레지스트리에 대 한 Azure Defender의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 742d230d6ed1535bada9c35219d2ddf68fd64ae1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977159"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>컨테이너 레지스트리에 대 한 Azure Defender 소개

ACR (Azure Container Registry)는 중앙 레지스트리에서 Azure 배포에 대 한 컨테이너 이미지를 저장 하 고 관리 하는 관리 되는 개인 Docker 레지스트리 서비스입니다. 오픈 소스 Docker 레지스트리 2.0을 기반으로 합니다.

구독의 모든 Azure Resource Manager 기반 레지스트리를 보호 하려면 구독 수준에서 **컨테이너 레지스트리에 대해 Azure Defender** 를 사용 하도록 설정 합니다. 그런 다음 레지스트리에 푸시되는 이미지 또는 최근 30 일 이내에 끌어온 이미지를 검색 Security Center 합니다. 이 기능은 이미지 별로 요금이 청구 됩니다.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>컨테이너 레지스트리에 대 한 Azure Defender의 이점은 무엇 인가요?

Security Center은 구독의 Azure Resource Manager 기반 ACR 레지스트리를 식별 하 고 레지스트리 이미지에 대 한 Azure native 취약성 평가 및 관리를 원활 하 게 제공 합니다.

**컨테이너 레지스트리에 대 한 Azure Defender** 에는 Azure Resource Manager 기반 Azure Container Registry 레지스트리의 이미지를 검색 하 고 이미지의 취약성에 대 한 심층적인 가시성을 제공 하는 취약성 스캐너가 포함 되어 있습니다. 통합 스캐너는 업계 최고의 취약성 검색 공급 업체 인 Qualys으로 구동 됩니다.

Qualys 또는 Security Center에서 문제가 발견 되 면 Security Center 대시보드의 알림이 표시 됩니다. 모든 취약점에 대해 Security Center는 심각도 분류 및 문제 해결 방법에 대 한 지침과 함께 조치 가능한 권장 사항을 제공 합니다. 컨테이너에 대 한 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항에 대 한 [참조 목록을](recommendations-reference.md#recs-containers)참조 하세요.

Security Center는 스캐너에서 검색 결과를 필터링 하 고 분류 합니다. 이미지가 정상 상태 이면 Security Center 표시 합니다. Security Center는 문제를 해결 해야 하는 이미지에 대해서만 보안 권장 사항을 생성 합니다. Security Center 보고 된 각 취약점 및 심각도 분류의 세부 정보를 제공 합니다. 또한 각 이미지에 있는 특정 취약점을 수정 하는 방법에 대 한 지침을 제공 합니다.

문제가 발생 하는 경우에만 Security Center 하면 원치 않는 정보 알림이 발생할 가능성이 줄어듭니다.


## <a name="when-are-images-scanned"></a>언제 이미지를 검색 하나요?

이미지 검색에 대 한 트리거는 세 가지가 있습니다.

- **푸시 시** -이미지를 레지스트리에 푸시할 때마다 Security Center에서 해당 이미지를 자동으로 검색 합니다. 이미지 검색을 트리거하려면 리포지토리에 푸시합니다.

- **최근 끌어온** -새 취약성이 매일 검색 되므로 **컨테이너 레지스트리에 대 한 Azure Defender** 는 지난 30 일 이내에 끌어온 모든 이미지를 검색 합니다. 다시 검사에 대 한 추가 비용은 없습니다. 위에서 설명한 것 처럼 이미지 마다 한 번씩 요금이 청구 됩니다.

- **에** 는 Docker 허브, Microsoft Container Registry 또는 다른 Azure Container registry에서 레지스트리에 이미지를 가져오는 가져오기 도구가 Azure Container Registry 있습니다. **컨테이너 레지스트리 용 Azure Defender** 는 가져온 모든 지원 되는 이미지를 검색 합니다. 컨테이너 [레지스트리에 컨테이너 이미지 가져오기](../container-registry/container-registry-import-images.md)에서 자세히 알아보세요.
 
검색은 일반적으로 2 분 이내에 완료 되지만 최대 15 분이 걸릴 수 있습니다. 결과는 다음과 같은 Security Center 권장 사항으로 제공 됩니다.

[![ACR (Azure Container Registry) 호스트 이미지에서 발견 된 취약성에 대 한 샘플 Azure Security Center 권장 사항](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Azure Container Registry에서 Security Center 작동 방법

다음은 Security Center를 사용 하 여 레지스트리를 보호 하는 경우의 구성 요소 및 이점에 대 한 개략적인 다이어그램입니다.

![ACR (Azure Security Center 및 Azure Container Registry) 개략적인 개요](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry 이미지 검색에 대 한 FAQ

### <a name="how-does-security-center-scan-an-image"></a>Security Center에서 이미지를 검색 하는 방법은 무엇입니까?
레지스트리에서 이미지를 가져옵니다. 그런 다음 알려진 취약점 목록을 추출 하는 Qualys 스캐너가 포함 된 격리 된 샌드박스에서 실행 됩니다.

Security Center는 스캐너에서 검색 결과를 필터링 하 고 분류 합니다. 이미지가 정상 상태 이면 Security Center 표시 합니다. Security Center는 문제를 해결 해야 하는 이미지에 대해서만 보안 권장 사항을 생성 합니다. 문제가 발생 하는 경우에만 Security Center 하면 원치 않는 정보 알림이 발생할 가능성이 줄어듭니다.

### <a name="how-often-does-security-center-scan-my-images"></a>에서 이미지를 얼마나 자주 Security Center 검색 하나요?
이미지 검색은 모든 푸시 또는 가져오기에서 트리거되고, 지난 30 일 내에 이미지를 끌어온 경우에 발생 합니다. 

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API를 통해 검사 결과를 가져올 수 있나요?
예. 결과는 [하위 평가 REST API](/rest/api/securitycenter/subassessments/list/)에 있습니다. 또한 모든 리소스에 대 한 Kusto like API 인 ARG (Azure 리소스 그래프)를 사용할 수 있습니다. 쿼리는 특정 검색을 페치할 수 있습니다.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>어떤 레지스트리 유형을 검색 하나요? 청구 되는 형식은 무엇입니까?
Availability 섹션에는 컨테이너 레지스트리에 대해 Azure Defender에서 지 원하는 컨테이너 레지스트리 유형이 나열 됩니다. 

지원 되지 않는 레지스트리를 Azure 구독에 연결 하는 경우 검색 되지 않으며 청구 되지 않습니다.


## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

- [Azure Security Center 및 컨테이너 보안](container-security.md)

- [Azure Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)


