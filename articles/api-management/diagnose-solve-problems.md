---
title: Azure API Management 진단 및 문제 해결
description: Azure Portal의 진단 및 해결 도구를 사용 하 여 Azure API Management에서 API 문제를 해결 하는 방법에 대해 알아봅니다.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652405"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API Management 진단 개요

Azure API Management에서 API를 빌드 및 관리 하는 경우 404 찾을 수 없음 오류에서 502 잘못 된 게이트웨이 오류까지 발생할 수 있는 모든 문제에 대해 준비 하려고 합니다. API Management 진단은 구성이 필요 없는 APIM에 게시 된 API 문제를 해결 하는 데 도움이 되는 지능적인 대화형 환경입니다. 게시 된 Api에 대 한 문제가 발생 하는 경우 API Management 진단은 문제를 해결 하 고 문제를 신속 하 게 해결 하는 데 적합 한 정보를 안내해 줍니다.

이 환경은 최근 24 시간 이내에 API 문제를 다시 발생 시킬 때 가장 유용 하지만 모든 진단 그래프가 항상 분석에 사용할 수 있습니다.

## <a name="open-api-management-diagnostics"></a>API Management 진단 열기

API Management 진단에 액세스 하려면 [Azure Portal](https://portal.azure.com)의 API Management 서비스 인스턴스로 이동 합니다. 왼쪽 탐색 영역에서 **문제 진단 및 해결** 을 선택 합니다.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="진단을 탐색 하는 방법을 보여 주는 스크린샷":::



## <a name="intelligent-search"></a>지능형 검색

페이지 맨 위에 있는 검색 표시줄에서 문제 또는 문제를 검색할 수 있습니다. 검색을 통해 문제를 해결 하거나 문제를 해결 하는 데 도움이 되는 도구를 찾을 수도 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="지능형 검색의 스크린샷":::


## <a name="troubleshooting-categories"></a>문제 해결 범주

범주에서 문제를 해결할 수 있습니다. API 성능, 게이트웨이, API 정책 및 서비스 계층과 관련 된 일반적인 문제는 각 범주 내에서 분석할 수 있습니다. 또한 각 범주는 더 구체적인 진단 검사를 제공 합니다. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="범주 개요의 스크린샷":::


### <a name="availability-and-performance"></a>가용성 및 성능

이 범주에서 API 가용성 및 성능 문제를 확인 합니다. 이 범주 타일을 선택한 후 대화형 인터페이스에서 몇 가지 일반적인 검사를 수행 하는 것을 볼 수 있습니다. 각 검사를 클릭 하 여 각 문제에 대 한 세부 정보를 자세히 확인 합니다. 이 검사에서는 API 성능 및 성능 문제에 대 한 요약을 보여 주는 그래프도 제공 합니다. 예를 들어 API 서비스에는 최종 시간에서 최종 시간에 5xx 오류가 발생 했을 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="대화형 인터페이스 확인의 스크린샷 1입니다.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="대화형 인터페이스 검사의 스크린샷 2":::

### <a name="api-policies"></a>API 정책

이 범주는 오류를 검색 하 여 정책 문제를 알려 줍니다. 

유사한 대화형 인터페이스는 API 정책 구성 문제를 해결 하는 데 도움이 되는 데이터 메트릭을 안내 합니다.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API 정책 범주 타일의 스크린샷":::

### <a name="gateway-performance"></a>게이트웨이 성능 

게이트웨이에서 게이트웨이 요청이 나 응답 또는 4xx 또는 5xx 오류가 발생 하면이 범주를 사용 하 여 모니터링 하 고 문제를 해결 합니다. 마찬가지로 대화형 인터페이스를 활용 하 여 API 게이트웨이 성능을 확인 하려는 특정 영역을 자세히 살펴볼 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="게이트웨이 성능 범주 타일의 스크린샷":::

### <a name="service-upgrade"></a>서비스 업그레이드

이 범주는 현재 사용 중인 서비스 계층 (SKU)을 확인 하 고 해당 계층과 관련 될 수 있는 문제를 방지 하기 위해 업그레이드를 알려 줍니다. 같은 대화형 인터페이스를 사용 하면 더 많은 그래픽 및 요약 확인 결과를 심층적으로 파악할 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="서비스 업그레이드 범주 타일의 스크린샷":::

## <a name="search-documentation"></a>설명서 검색

문제 진단 및 해결 도구에 대 한 추가 정보를 참조 하 여 문제와 관련 된 문제 해결 설명서를 검색할 수 있습니다. 서비스에서 진단을 실행 한 후 대화형 인터페이스에서 **설명서 검색** 을 선택 합니다. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="검색 설명서 함수를 사용 하는 방법의 스크린샷 1입니다.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="검색 설명서를 사용 하는 방법의 스크린샷 2":::


## <a name="next-steps"></a>다음 단계

* [Api analytics](howto-use-analytics.md) 를 사용 하 여 api의 사용량 및 성능을 분석 합니다. 
* 진단을 사용 하 여 Web Apps 문제를 해결 하 시겠습니까? [여기](../app-service/overview-diagnostics.md) 에서 읽기
* 진단을 활용 하 여 Azure Kubernetes Services 문제를 확인 합니다. [AKS의 진단](../aks/concepts-diagnostics.md) 참조
* 제목에 "[Diag]"를 추가 하 여 [UserVoice](https://feedback.azure.com/forums/248703-api-management) 에서 질문이 나 의견을 게시 합니다.
