---
title: 배포 및 성능 최적화
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 성능을 조정 하 고 최적 규모를 구성 하는 방법 및 모범 사례를 알아봅니다.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6779843546bd41423c140ef40de6a7e24b026
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113255"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Azure Cognitive Search의 성능 최적화를 위한 배포 전략 및 모범 사례

이 문서에서는 확장성 및 가용성을 위한 정교한 요구 사항이 있는 고급 시나리오에 대 한 모범 사례를 설명 합니다. 

## <a name="develop-baseline-numbers"></a>기준 번호 개발
검색 성능을 최적화 하는 경우 쿼리 실행 시간을 줄이는 데 집중 해야 합니다. 이렇게 하려면 일반적인 쿼리 로드 모양에 대해 알고 있어야 합니다. 다음 지침은 기준 쿼리 번호에 도달 하는 데 도움이 될 수 있습니다.

1. 일반적인 검색 요청을 완료하는 데 걸리는 목표 대기 시간(또는 최대 시간)을 선택합니다.
2. 실제 데이터 세트를 사용하여 검색 서비스에 대한 실제 작업을 만들고 테스트하여 이러한 대기 시간 속도를 측정합니다.
3. QPS (초당 쿼리 수)로 시작 하 고 쿼리 대기 시간이 정의 된 대상 대기 시간 아래로 떨어질 때까지 테스트에서 실행 된 수를 점차적으로 늘립니다. 이러한 방식은 애플리케이션 사용량이 증가함에 따라 확장을 계획하는 데 도움이 되는 중요한 벤치마크입니다.
4. 가능하면 HTTP 연결을 다시 사용합니다. Azure Cognitive Search .NET SDK를 사용 하는 경우 인스턴스 또는 [Searchindexclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스를 다시 사용 해야 하며, REST API를 사용 하는 경우 단일 httpclient를 다시 사용 해야 합니다.
5. 인덱스의 여러 부분에 대해 검색을 수행 하도록 쿼리 요청을 다양 하 게 변경 합니다. 동일한 검색 요청을 계속 해 서 실행 하는 경우 데이터의 캐싱이 보다 개별 쿼리 집합을 사용 하는 것 보다 성능이 향상 되기 시작 하기 때문에 변형이 중요 합니다.
6. 쿼리 요청의 구조를 변경 하 여 다양 한 유형의 쿼리를 가져옵니다. 모든 검색 쿼리가 같은 수준에서 수행 되는 것은 아닙니다. 예를 들어 문서 조회 또는 검색 제안은 일반적으로 많은 수의 패싯 및 필터를 포함 하는 쿼리 보다 빠릅니다. 테스트 컴포지션에는 프로덕션에서 짐작할 수 있는 것과 거의 동일한 비율의 다양 한 쿼리가 포함 되어야 합니다.  

이러한 테스트 작업을 만드는 동안 Azure Cognitive Search의 몇 가지 특성을 염두에 두어야 합니다.

+ 한 번에 너무 많은 검색 쿼리를 푸시하여 서비스를 오버 로드할 수 있습니다. 이 경우 HTTP 503 응답 코드가 표시됩니다. 테스트 하는 동안 503을 방지 하려면 다양 한 검색 요청을 시작 하 여 더 많은 검색 요청을 추가할 때 대기 시간 비율의 차이를 확인 합니다.

+ Azure Cognitive Search은 백그라운드에서 인덱싱 작업을 실행 하지 않습니다. 서비스에서 쿼리 및 인덱싱 워크 로드를 동시에 처리 하는 경우 쿼리 테스트에 인덱싱 작업을 도입 하거나 사용량이 적은 시간에 인덱싱 작업을 실행 하기 위한 옵션을 탐색 하 여이를 고려 합니다.

> [!NOTE]
> [Visual Studio 부하 테스트](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) 는 Azure Cognitive Search에 대해 쿼리를 실행 하는 데 필요한 것 처럼 HTTP 요청을 실행할 수 있도록 하 고 요청을 병렬화 할 수 있도록 하는 매우 좋은 방법입니다.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>높은 쿼리 볼륨 및 제한 된 요청에 대 한 크기 조정
제한 된 요청을 너무 많이 받거나 쿼리 부하가 증가 하 여 대상 대기 시간 비율을 초과 하는 경우 다음 두 가지 방법 중 하나로 대기 시간 비율을 낮출 수 있습니다.

1. **복제본 증가:**  복제본은 Azure Cognitive Search에서 여러 복사본에 대 한 요청의 부하를 분산할 수 있도록 하는 데이터의 복사본과 비슷합니다.  복제본 간의 모든 부하 분산 및 복제는 Azure Cognitive Search에서 관리 하며, 언제 든 지 서비스에 할당 된 복제본 수를 변경할 수 있습니다.  표준 검색 서비스에서는 최대 12개, 기본 검색 서비스에서는 최대 3개의 복제본을 할당할 수 있습니다. 복제본은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.
2. **검색 계층 증가:**  Azure Cognitive Search는 여러 [계층](https://azure.microsoft.com/pricing/details/search/) 으로 제공 되며, 이러한 각 계층은 다양 한 수준의 성능을 제공 합니다.  일부 경우에는 복제본이 최대값 된 경우에도, 계층에서 충분 한 대기 시간 비율을 제공할 수 없는 쿼리가 많은 경우가 있습니다. 이 경우 많은 수의 문서와 매우 높은 쿼리 워크 로드를 포함 하는 시나리오에 적합 한 Azure Cognitive Search S3 계층과 같은 상위 검색 계층 중 하나를 활용 하는 것이 좋습니다.

## <a name="scaling-for-slow-individual-queries"></a>저속 개별 쿼리를 위한 크기 조정
대기 시간이 긴 또 다른 이유는 단일 쿼리를 완료 하는 데 너무 오래 걸립니다. 이 경우 복제본을 추가 하는 것은 도움이 되지 않습니다. 다음을 포함 하는 두 가지 옵션을 사용할 수 있습니다.

1. **파티션 증가** 파티션은 추가 리소스로 데이터를 분할하는 메커니즘입니다. 두 번째 파티션을 추가 하면 데이터는 두 개로 분할 되 고 세 번째 파티션은 3 개로 분할 됩니다. 한 가지 긍정 부작용은 병렬 컴퓨팅으로 인해 느린 쿼리가 때때로 더 빠르게 수행 되는 것입니다. 많은 문서와 일치 하는 쿼리 또는 많은 문서에 대 한 개수를 제공 하는 패싯에 대해 낮은 선택이 가능한 쿼리를 병렬화 했습니다. 문서 관련성을 점수를 매기는 데 상당한 계산이 필요 하기 때문에 문서 수를 계산 하기 위해 여분의 파티션을 추가 하면 쿼리가 더 빨리 완료 됩니다.  
   
   표준 검색 서비스의 파티션 제한은 12개이고, 기본 검색 서비스의 파티션 제한은 1개입니다.  파티션은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

2. **높은 카디널리티 필드 제한:** 높은 카디널리티 필드는 많은 수의 고유 값을 포함 하는 패싯 가능 또는 필터링 가능한 필드로 구성 되며 결과를 계산할 때 상당한 리소스를 소비 합니다. 예를 들어 패싯 가능/필터링 가능으로 제품 ID 또는 설명 필드를 설정 하는 것은 문서와 문서에 있는 대부분의 값이 고유 하기 때문에 높은 카디널리티로 계산 됩니다. 가능한 경우 높은 카디널리티 필드의 수를 제한하도록 합니다.

3. **검색 계층 증가:**  더 높은 Azure Cognitive Search 계층으로 이동 하는 것은 속도가 느려지는 쿼리 성능을 향상 시키는 또 다른 방법일 수 있습니다. 각 상위 계층은 더 빠른 Cpu와 더 많은 메모리를 제공 하며, 둘 다 쿼리 성능에 긍정적인 영향을 줍니다.

## <a name="scaling-for-availability"></a>가용성에 따른 크기 조정
복제본은 쿼리 대기 시간을 줄이는 데 도움이 될 뿐만 아니라 고가용성도 가능하게 합니다. 단일 복제본을 사용하는 경우 소프트웨어 업데이트 이후의 서버 다시 부팅 또는 발생하게 되는 기타 유지 관리 이벤트로 인한 주기적인 작동 중지가 예상될 수 있습니다.  결과적으로 애플리케이션이 높은 검색(쿼리) 및 쓰기(인덱싱 이벤트) 가용성을 요구하는지를 고려하는 것이 중요합니다. Azure Cognitive Search는 다음 특성을 사용 하 여 모든 유료 검색 제품에 대 한 SLA 옵션을 제공 합니다.

* 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 2개
* 읽기/쓰기 작업(쿼리 및 인덱싱)의 고가용성을 위한 복제본 3개 이상

이에 대 한 자세한 내용은 [Azure Cognitive Search Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 참조 하세요.

복제본이 데이터의 복사본 이므로 여러 복제본을 사용 하면 Azure Cognitive Search에서 한 복제본에 대해 컴퓨터 재부팅 및 유지 관리를 수행 하는 동시에 다른 복제본에서 쿼리 실행을 계속할 수 있습니다. 반대로 복제본을 사용 하지 않는 경우 해당 복제본이 미달 사용 리소스인 것으로 가정 하 여 쿼리 성능이 저하 될 수 있습니다.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>지리적으로 분산 되는 워크 로드 및 지리적 중복성을 위한 크기 조정
지리적으로 분산 된 워크 로드의 경우 Azure Cognitive Search를 호스트 하는 데이터 센터에서 멀리 떨어져 있는 사용자는 더 높은 대기 시간 요금이 부과 됩니다. 한 가지 완화 방법은 이러한 사용자에 게 더 가까운 지역에서 여러 검색 서비스를 프로 비전 하는 것입니다. Azure Cognitive Search는 현재 지역 간에 Azure Cognitive Search 인덱스를 지리적으로 복제 하는 자동화 된 방법을 제공 하지 않지만,이 프로세스를 간단 하 게 구현 하 고 관리 하는 데 사용할 수 있는 몇 가지 기술이 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

지리적으로 분산 된 search 서비스 집합의 목표는 두 개 이상의 지역에서 두 개 이상의 인덱스를 사용할 수 있도록 하는 것입니다 .이 예제에서 볼 수 있는 것 처럼 가장 낮은 대기 시간을 제공 하는 Azure Cognitive Search 서비스로 사용자가 라우팅됩니다.

   ![지역별 서비스 크로스탭][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>여러 Azure Cognitive Search 서비스에서 데이터를 동기화 상태로 유지
분산 검색 서비스를 동기화 상태로 유지 하는 두 가지 옵션은 [azure Cognitive Search 인덱서](search-indexer-overview.md) 또는 밀어넣기 API ( [azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)라고도 함)를 사용 하는 것으로 구성 됩니다.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>여러 서비스에서 콘텐츠를 업데이트 하는 데 인덱서 사용

한 서비스에서 이미 인덱서를 사용 하 고 있는 경우 같은 데이터 원본 개체를 사용 하 여 동일한 위치에서 데이터를 끌어오는 두 번째 서비스에서 두 번째 인덱서를 구성할 수 있습니다. 각 지역의 각 서비스에는 고유한 인덱서 및 대상 인덱스가 있습니다. 검색 인덱스는 공유 되지 않으므로 (데이터가 중복 됨) 각 인덱서는 동일한 데이터 원본을 참조 합니다.

아키텍처의 모양에 대 한 개략적인 시각적 개체는 다음과 같습니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>REST Api를 사용 하 여 여러 서비스에 콘텐츠 업데이트 푸시
Azure Cognitive Search REST API를 사용 하 여 [azure Cognitive Search 인덱스에 콘텐츠를 푸시하](https://docs.microsoft.com/rest/api/searchservice/update-index)는 경우 업데이트가 필요할 때마다 모든 search 서비스에 변경 내용을 푸시하여 다양 한 검색 서비스를 동기화 된 상태로 유지할 수 있습니다. 코드에서 한 검색 서비스에 대 한 업데이트가 실패 하지만 다른 검색 서비스에 대해 성공 하는 경우를 처리 해야 합니다.

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager 활용
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 를 사용 하면 여러 개의 검색 서비스에서 지원 되는 여러 지리적 위치 웹 사이트로 요청을 라우팅할 수 있습니다. Traffic Manager의 이점 중 하나는 Azure Cognitive Search를 검색 하 여 사용 가능한 지 확인 하 고 가동 중지 시간 발생 시 사용자를 대체 검색 서비스로 라우팅합니다. 또한 azure 웹 사이트를 통해 검색 요청을 라우팅하는 경우 Azure Traffic Manager를 사용 하 여 웹 사이트가 있지만 Azure Cognitive Search 아닌 경우의 부하를 분산할 수 있습니다. Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="next-steps"></a>다음 단계
각 항목에 대 한 가격 책정 계층 및 서비스 제한에 대 한 자세한 내용은 [Azure Cognitive Search의 서비스 제한](search-limits-quotas-capacity.md)을 참조 하세요.

파티션 및 복제본 조합에 대해 자세히 알아보려면 [용량 계획](search-capacity-planning.md) 을 참조하세요.

성능에 대해 좀 더 드릴다운하고 이 문서에 설명된 최적화 구현 방법의 추가적인 예시를 보려면 다음 동영상을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
