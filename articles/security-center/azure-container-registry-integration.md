---
title: Azure Security Center 및 Azure Container Registry
description: Azure Security Center를 사용 하 여 컨테이너 레지스트리를 검색 하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977354"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Security Center Azure Container Registry 이미지 검색

ACR (Azure Container Registry)는 중앙 레지스트리에서 Azure 배포에 대 한 컨테이너 이미지를 저장 하 고 관리 하는 관리 되는 개인 Docker 레지스트리 서비스입니다. 오픈 소스 Docker 레지스트리 2.0을 기반으로 합니다.

**컨테이너 레지스트리에 대 한 Azure Defender** 를 사용 하도록 설정 하 여 Azure Resource Manager 기반 레지스트리의 이미지 취약점을 보다 자세히 파악할 수 있습니다. 구독 수준에서 계획을 사용 하거나 사용 하지 않도록 설정 하 여 구독에 있는 모든 레지스트리를 포함 합니다. [가격 책정 페이지](security-center-pricing.md)에 표시 된 것 처럼이 기능은 이미지 별로 요금이 청구 됩니다. Azure Defender를 사용 하도록 설정 하면 Security Center 레지스트리에 푸시되는 이미지를 스캔할 수 있습니다. 


## <a name="when-are-images-scanned"></a>언제 이미지를 검색 하나요?

이미지를 레지스트리에 푸시할 때마다 Security Center에서 해당 이미지를 자동으로 검색 합니다. 이미지 검색을 트리거하려면 리포지토리에 푸시합니다.

검색이 완료 될 때 (일반적으로 약 2 분 후에는 최대 15 분이 될 수 있음) 다음과 같은 Security Center 권장 사항으로 결과를 사용할 수 있습니다.

[![ACR (Azure Container Registry) 호스트 이미지에서 발견 된 취약성에 대 한 샘플 Azure Security Center 권장 사항](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>통합의 이점

Security Center는 구독에서 Azure Resource Manager 기반 ACR 레지스트리를 식별 하 고 원활 하 게 제공 합니다.

* 푸시된 모든 Linux 이미지에 대 한 **Azure native 취약성 검색** Security Center 업계 최고의 취약점 검색 공급 업체 (Qualys)의 스캐너를 사용 하 여 이미지를 검색 합니다. 이 네이티브 솔루션은 기본적으로 원활 하 게 통합 됩니다.

* 알려진 취약성을 포함 하는 Linux 이미지에 대 한 **보안 권장 사항** Security Center 보고 된 각 취약점 및 심각도 분류의 세부 정보를 제공 합니다. 또한 레지스트리에 푸시되는 각 이미지에 있는 특정 취약점을 수정 하는 방법에 대 한 지침을 제공 합니다.

![ACR (Azure Security Center 및 Azure Container Registry) 개략적인 개요](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry 이미지 검색에 대 한 FAQ

### <a name="how-does-security-center-scan-an-image"></a>Security Center에서 이미지를 검색 하는 방법은 무엇입니까?
레지스트리에서 이미지를 가져옵니다. 그런 다음 알려진 취약점 목록을 추출 하는 Qualys 스캐너가 포함 된 격리 된 샌드박스에서 실행 됩니다.

Security Center는 스캐너에서 검색 결과를 필터링 하 고 분류 합니다. 이미지가 정상 상태 이면 Security Center 표시 합니다. Security Center는 문제를 해결 해야 하는 이미지에 대해서만 보안 권장 사항을 생성 합니다. 문제가 발생 하는 경우에만 Security Center 하면 원치 않는 정보 알림이 발생할 가능성이 줄어듭니다.

### <a name="how-often-does-security-center-scan-my-images"></a>에서 이미지를 얼마나 자주 Security Center 검색 하나요?
이미지 검색은 푸시 마다 트리거됩니다.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API를 통해 검사 결과를 가져올 수 있나요?
예. 결과는 [하위 평가 REST API](/rest/api/securitycenter/subassessments/list/)에 있습니다. 또한 모든 리소스에 대 한 Kusto like API 인 ARG (Azure 리소스 그래프)를 사용할 수 있습니다. 쿼리는 특정 검색을 페치할 수 있습니다.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>어떤 레지스트리 유형을 검색 하나요? 청구 되는 형식은 무엇입니까?
Availability 섹션에는 컨테이너 레지스트리에 대해 Azure Defender에서 지 원하는 컨테이너 레지스트리 유형이 나열 됩니다. 

지원 되지 않는 레지스트리는 Azure 구독에 연결 되어 있으면 검색 되지 않으며 요금이 청구 되지 않습니다.


## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Kubernetes Service와 통합](azure-kubernetes-service-integration.md)


