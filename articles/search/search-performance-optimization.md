---
title: 성능 확장
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 성능을 조정하고 최적의 규모를 구성하기 위한 기술 및 모범 사례를 알아봅니다.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212378"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure 인지 검색의 성능을 위한 확장

이 문서에서는 확장성 및 가용성에 대한 정교한 요구 사항이 있는 고급 시나리오에 대한 모범 사례에 대해 설명합니다.

## <a name="start-with-baseline-numbers"></a>기준 번호로 시작

더 큰 배포 를 시작하기 전에 일반적인 쿼리 로드가 어떻게 보이는지 알고 있는지 확인하십시오. 다음 지침은 기준 쿼리 번호에 도달하는 데 도움이 될 수 있습니다.

1. 일반적인 검색 요청을 완료하는 데 걸리는 목표 대기 시간(또는 최대 시간)을 선택합니다.

1. 이러한 대기 시간 속도를 측정하기 위해 현실적인 데이터 집합을 사용하여 검색 서비스에 대해 실제 워크로드를 만들고 테스트합니다.

1. 낮은 초당 쿼리 수(QPS)로 시작한 다음 쿼리 대기 시간이 미리 정의된 대상 아래로 떨어질 때까지 테스트에서 실행된 수를 점진적으로 늘립니다. 이러한 방식은 애플리케이션 사용량이 증가함에 따라 확장을 계획하는 데 도움이 되는 중요한 벤치마크입니다.

1. 가능하면 HTTP 연결을 다시 사용합니다. Azure 인지 검색 .NET SDK를 사용 하는 경우 즉 인스턴스 또는 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스를 다시 사용 해야 하 고 나머지 API를 사용 하는 경우 단일 HttpClient를 다시 사용 해야 합니다.

1. 인덱스의 다른 부분에서 검색이 발생되도록 쿼리 요청의 실체를 변경합니다. 동일한 검색 요청을 계속 실행하는 경우 데이터 캐싱이 보다 다른 쿼리 집합을 사용하면 성능이 향상되기 시작하기 때문에 변형이 중요합니다.

1. 다양한 유형의 쿼리를 얻을 수 있도록 쿼리 요청의 구조를 변경합니다. 모든 검색 쿼리가 동일한 수준에서 수행되는 것은 아닙니다. 예를 들어 문서 조회 또는 검색 제안은 일반적으로 많은 수의 면및 필터가 있는 쿼리보다 빠릅니다. 테스트 컴포지션에는 프로덕션 환경에서 예상한 것과 거의 동일한 비율로 다양한 쿼리가 포함되어야 합니다.  

이러한 테스트 워크로드를 만드는 동안 염두에 두어야 할 Azure Cognitive Search의 몇 가지 특성이 있습니다.

+ 한 번에 너무 많은 검색 쿼리를 푸시하여 서비스에 과부하가 초과 될 수 있습니다. 이 경우 HTTP 503 응답 코드가 표시됩니다. 테스트 중에 503을 방지하려면 다양한 검색 요청범위로 시작하여 검색 요청을 더 추가할 때 대기 시간 속도의 차이를 확인합니다.

+ Azure 인지 검색백그라운드에서 인덱싱 작업을 실행하지 않습니다. 서비스가 쿼리 및 인덱싱 워크로드를 동시에 처리하는 경우 쿼리 테스트에 인덱싱 작업을 도입하거나 사용량이 많은 시간에 인덱싱 작업을 실행하기 위한 옵션을 탐색하여 이 점을 고려합니다.

> [!Tip]
> 부하 테스트 도구를 사용하여 사실적인 쿼리 부하를 시뮬레이션할 수 있습니다. [Azure DevOps를 사용하여 로드 테스트를](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) 시도하거나 다음 [대안](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)중 하나를 사용합니다.

## <a name="scale-for-high-query-volume"></a>높은 쿼리 볼륨에 대한 확장

쿼리가 너무 오래 걸리거나 서비스가 요청을 삭제하기 시작할 때 서비스가 과부하가 걸리게 됩니다. 이 경우 다음 두 가지 방법 중 하나로 문제를 해결할 수 있습니다.

+ **복제본 추가**  

  각 복제본은 데이터의 복사본으로, 서비스가 여러 복사본에 대해 균형 요청을 로드할 수 있도록 합니다.  데이터의 모든 로드 균형 조정 및 복제는 Azure Cognitive Search에서 관리하며 언제든지 서비스에 할당된 복제본 수를 변경할 수 있습니다. 표준 검색 서비스에서는 최대 12개, 기본 검색 서비스에서는 최대 3개의 복제본을 할당할 수 있습니다. 복제본은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

+ **상위 계층에서 새 서비스 만들기**  

  Azure Cognitive Search는 [여러 계층으로](https://azure.microsoft.com/pricing/details/search/) 제공되며 각 계층은 서로 다른 수준의 성능을 제공합니다. 경우에 따라 사용 중인 계층이 복제본이 최대화되더라도 충분한 처리 시간을 제공할 수 없는 쿼리가 너무 많을 수 있습니다. 이 경우 많은 수의 문서와 매우 높은 쿼리 워크로드가 있는 시나리오를 위해 설계된 표준 S3 계층과 같은 더 높은 성능의 계층으로 이동하는 것이 좋습니다.

## <a name="scale-for-slow-individual-queries"></a>느린 개별 쿼리에 맞게 확장

대기 시간이 많은 또 다른 이유는 단일 쿼리를 완료하는 데 시간이 너무 오래 걸리기 때문입니다. 이 경우 복제본을 추가하는 것은 도움이 되지 않습니다. 다음을 포함하는 데 도움이 될 수 있는 두 가지 가능한 옵션:

+ **파티션 늘리기**

  파티션은 추가 컴퓨팅 리소스간에 데이터를 분할합니다. 두 개의 파티션은 데이터를 반으로 나누고 세 번째 파티션은 데이터를 3분의 1로 분할합니다. 한 가지 긍정적인 부작용은 병렬 컴퓨팅으로 인해 느린 쿼리가 더 빠르게 수행되는 경우가 있다는 것입니다. 많은 문서와 일치하는 쿼리 또는 많은 수의 문서에 대한 개수를 제공하는 패싯과 같이 선택성이 낮은 쿼리에서 병렬화가 있음을 언급했습니다. 문서의 관련성을 점수로 채점하거나 문서 수를 계산하려면 상당한 계산이 필요하므로 파티션을 추가하면 쿼리를 더 빠르게 완료할 수 있습니다.  
   
  표준 검색 서비스에는 최대 12개의 파티션이 있고 기본 검색 서비스에는 파티션이 1개 있을 수 있습니다. 파티션은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

+ **높은 카디널리티 필드 제한**

  높은 카디널리티 필드는 고유 값이 상당수 인 면 테이블 또는 필터링 가능한 필드로 구성되며 결과적으로 결과를 계산할 때 상당한 리소스를 소비합니다. 예를 들어 제품 ID 또는 설명 필드를 얼굴 테이블/필터링 가능으로 설정하면 문서에서 문서로의 대부분의 값이 고유하기 때문에 카디널리티가 높은 것으로 계산됩니다. 가능한 경우 높은 카디널리티 필드의 수를 제한하도록 합니다.

+ **검색 계층 증가**  

  더 높은 Azure 인지 검색 계층으로 이동하는 것은 느린 쿼리의 성능을 향상시키는 또 다른 방법이 될 수 있습니다. 상위 계층마다 더 빠른 CPU와 더 많은 메모리를 제공하므로 쿼리 성능에 긍정적인 영향을 미칩니다.

## <a name="scale-for-availability"></a>가용성에 맞게 확장

복제본은 쿼리 대기 시간을 줄이는 데 도움이 될 뿐만 아니라 고가용성을 허용할 수도 있습니다. 단일 복제본을 사용하는 경우 소프트웨어 업데이트 이후의 서버 다시 부팅 또는 발생하게 되는 기타 유지 관리 이벤트로 인한 주기적인 작동 중지가 예상될 수 있습니다. 결과적으로 애플리케이션이 높은 검색(쿼리) 및 쓰기(인덱싱 이벤트) 가용성을 요구하는지를 고려하는 것이 중요합니다. Azure Cognitive Search는 다음과 같은 특성을 가진 모든 유료 검색 제품에 대한 SLA 옵션을 제공합니다.

+ 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 두 개

+ 읽기-쓰기 워크로드(쿼리 및 인덱싱)의 고가용성을 위한 세 개 이상의 복제본

이에 대한 자세한 내용은 [Azure 인지 검색 서비스 수준 계약을](https://azure.microsoft.com/support/legal/sla/search/v1_0/)참조하십시오.

복제본은 데이터의 복사본이므로 여러 복제본을 사용하면 Azure Cognitive Search에서 한 복제본에 대해 컴퓨터 재부팅 및 유지 관리를 수행할 수 있으며 쿼리 실행은 다른 복제본에서 계속됩니다. 반대로 복제본을 제거하면 해당 복제본이 사용률이 낮은 리소스라고 가정하여 쿼리 성능이 저하됩니다.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>지리적 분산 워크로드 및 지리적 중복성에 대한 확장

지역 분산 워크로드의 경우 호스트 데이터 센터에서 멀리 떨어진 곳에 있는 사용자는 대기 시간이 더 높습니다. 한 가지 완화 방법은 이러한 사용자와 가까운 지역에서 여러 검색 서비스를 프로비전하는 것입니다.

Azure Cognitive Search는 현재 여러 지역에서 Azure Cognitive Search 인덱스를 지리적으로 복제하는 자동화된 방법을 제공하지 않지만 이 프로세스를 구현하고 관리하기 쉽게 만들 수 있는 몇 가지 기술을 사용할 수 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

지리적으로 분산된 검색 서비스 집합의 목표는 두 개 이상의 지역에서 두 개 이상의 인덱스를 사용할 수 있도록 하는 것입니다.

   ![지역별 서비스 크로스탭][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>여러 서비스 간에 데이터 동기화 유지

분산 검색 서비스를 동기화상태로 유지하는 방법에는 [Azure 인지 검색 인덱서](search-indexer-overview.md) 또는 푸시 API(Azure 인지 [검색 REST API라고도](https://docs.microsoft.com/rest/api/searchservice/)함)를 사용하는 두 가지 옵션이 있습니다.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>여러 서비스에서 콘텐츠를 업데이트하기 위해 인덱서 사용

한 서비스에서 이미 인덱서를 사용하고 있는 경우 두 번째 서비스에서 두 번째 인덱서를 구성하여 동일한 데이터 원본 개체를 사용하여 동일한 위치에서 데이터를 끌어낼 수 있습니다. 각 리전의 각 서비스에는 고유한 인덱서와 대상 인덱스(검색 인덱스가 공유되지 않으므로 데이터가 중복됨)가 있지만 각 인덱서가 동일한 데이터 원본을 참조합니다.

다음은 해당 아키텍처의 모양에 대한 높은 수준의 시각적 개체입니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>여러 서비스에서 콘텐츠 업데이트를 푸시하기 위해 REST API 사용

Azure 인지 검색 REST API를 사용하여 [Azure Cognitive Search 인덱스의 콘텐츠를 푸시하는](https://docs.microsoft.com/rest/api/searchservice/update-index)경우 업데이트가 필요할 때마다 모든 검색 서비스에 변경 내용을 푸시하여 다양한 검색 서비스를 동기화상태로 유지할 수 있습니다. 코드에서 한 검색 서비스에 대한 업데이트가 실패하지만 다른 검색 서비스에 성공하는 경우를 처리해야 합니다.

## <a name="leverage-azure-traffic-manager"></a>Azure 트래픽 관리자 활용

[Azure Traffic Manager를](../traffic-manager/traffic-manager-overview.md) 사용하면 요청을 여러 지리적 위치에 있는 웹 사이트로 라우팅한 다음 여러 검색 서비스에서 백업할 수 있습니다. 트래픽 관리자의 한 가지 장점은 Azure Cognitive Search를 검색하여 사용 가능한지 확인하고 가동 중지 시 사용자를 대체 검색 서비스로 라우팅할 수 있다는 것입니다. 또한 Azure 웹 사이트를 통해 검색 요청을 라우팅하는 경우 Azure 트래픽 관리자를 사용하면 웹 사이트가 가동중이지만 Azure Cognitive Search가 아닌 균형 서비스 케이스를 로드할 수 있습니다. Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="next-steps"></a>다음 단계

각 등급 및 서비스 제한에 대한 자세한 내용은 [서비스 제한을](search-limits-quotas-capacity.md)참조하십시오. 파티션 및 복제본 [조합에 대해](search-capacity-planning.md) 자세히 알아보려면 용량 계획을 참조하십시오.

이 문서에서 설명하는 기술의 성능 및 데모에 대한 설명은 다음 비디오를 참조하십시오.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
