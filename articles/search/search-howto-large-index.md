---
title: Azure Search 인덱스 다시 빌드 또는 검색 가능한 콘텐츠 새로 고침 | Microsoft Docs
description: 전체 다시 빌드 또는 부분 증분 인덱싱에서 새 요소를 추가하거나 기존 요소 또는 문서를 업데이트하거나 오래된 문서를 삭제하여 Azure Search 인덱스를 새로 고칩니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 0dd7a5d5159144c6b1a050ff4c0443b181976738
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124957"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Azure Search에서 인덱싱을 스케일 아웃하는 방법

데이터 볼륨이 커지거나 처리 요구 사항이 변경되면 간단한 [다시 빌드 및 다시 인덱싱 작업](search-howto-reindex.md)만으로는 충분하지 않을 수 있습니다. 

늘어나는 수요를 충족시키기 위한 첫 번째 단계로 기존 서비스의 한도 내에서 [규모와 용량](search-capacity-planning.md)을 증가시키는 것이 좋습니다. 

두 번째 단계에서는 [인덱서](search-indexer-overview.md)를 사용할 수 있는 경우 확장 가능한 인덱싱에 대한 메커니즘을 추가합니다. 인덱서에는 일정한 간격으로 인덱싱을 처리하거나 24시간 범위를 초과하여 처리를 확장할 수 있는 기본 제공 스케줄러가 함께 제공됩니다. 또한 데이터 원본 정의와 쌍을 이루어 인덱서를 사용하여 데이터를 분할하고 병렬로 실행하는 일정을 사용하여 병렬 처리를 구현할 수 있습니다.

### <a name="scheduled-indexing-for-large-data-sets"></a>큰 데이터 집합에 대한 인덱싱 예약

일정 예약은 큰 데이터 집합을 처리하고 Cognitive Search 파이프라인에서 이미지 분석과 같이 느리게 실행되는 분석을 처리하는 중요한 메커니즘입니다. 인덱서 처리는 24시간 범위 내에 실행됩니다. 24시간 내에 처리가 완료되지 않으면 인덱서 일정 예약의 동작이 사용자에게 유리하게 작용할 수 있습니다. 

기본적으로 예약된 인덱싱은 특정 간격으로 시작되며, 대개 다음으로 예약된 간격에서 다시 시작되기 전에 작업이 완료됩니다. 하지만 간격 내에 처리가 완료되지 않으면 인덱서가 중지됩니다(시간이 초과되기 때문임). 다음 번 간격이 시작되면, 마지막으로 중단된 위치에서 처리가 다시 시작되며, 시스템은 해당 위치를 추적합니다. 

실제 여러 날에 걸쳐 수행되는 인덱스 로드의 경우 24시간 일정으로 인덱서를 배치할 수 있습니다. 다음 번 24시간 범위에서 인덱싱이 재개되면 마지막으로 성공한 문서에서 다시 시작됩니다. 이러한 방식으로 인덱서는 처리되지 않은 모든 문서가 처리될 때까지 여러 날에 걸쳐 문서 백로그를 통해 작업을 수행할 수 있습니다. 이러한 방식에 대한 자세한 내용은 [큰 데이터 집합 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)을 참조하세요.

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>병렬 인덱싱

두 번째 방법은 병렬 인덱싱 전략을 설정하는 것입니다. 일상적이지 않고 계산 집약적인 인덱싱 요구 사항(예: Cognitive Search 파이프라인에서 검색된 문서의 OCR)의 경우, 병렬 인덱싱 전략이 이러한 특정 목적에 적합한 방법일 수 있습니다. Cognitive Search 보강 파이프라인에서 이미지 분석과 자연어 처리가 장기간 실행됩니다. 쿼리 요청을 동시에 처리하지 않는 서비스에 대한 병렬 인덱싱은 처리 속도가 느린 대량의 콘텐츠에 적절한 옵션이 될 수 있습니다. 

병렬 처리 전략에는 다음과 같은 요소가 있습니다.

+ 원본 데이터를 동일한 컨테이너 내의 여러 컨테이너 또는 여러 가상 폴더로 나눕니다. 
+ 각각의 미니 데이터 집합을 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에 매핑하고, 자체 [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer)와 쌍을 이루도록 합니다.
+ Cognitive Search의 경우 각 인덱서 정의에 동일한 [기술 집합](https://docs.microsoft.com/rest/api/searchservice/create-skillset)을 참조합니다.
+ 동일한 대상 검색 인덱스에 기록합니다. 
+ 모든 인덱서가 동시에 실행되도록 예약합니다.

> [!Note]
> Azure Search는 복제본이나 파티션을 특정 워크로드 전용으로 지원하지 않습니다. 동시 인덱싱이 과도할 경우 발생할 수 있는 문제는 시스템에 과부하가 발생하여 쿼리 성능이 저하되는 것입니다. 테스트 환경이 있는 경우, 먼저 장단점을 파악할 수 있도록 병렬 인덱싱을 구현하십시오.

## <a name="configure-parallel-indexing"></a>병렬 인덱싱 구성

인덱서의 경우 처리 용량은 검색 서비스에 사용되는 각 SU(서비스 단위)에 대해 하나의 인덱서 하위 시스템을 기반으로 합니다. 다수의 동시 인덱서는 복제본이 2개 이상 있는 기본 또는 표준 계층에 프로비전된 Azure Search 서비스에서 가능합니다. 

1. [Azure Portal](https://portal.azure.com)의 검색 서비스 대시보드 **개요** 페이지에서 **가격 책정 계층**을 확인하여 병렬 인덱싱을 수용할 수 있는지 확인합니다. 기본 및 표준 계층 모두 여러 복제본이 제공됩니다.

2. **설정** > **규모**에서 병렬 처리를 위해 인덱서 워크로드마다 복제본을 하나씩 추가하여 [복제본을 증가](search-capacity-planning.md)시킵니다. 기존 쿼리 볼륨에 대해 충분한 수를 유지합니다. 인덱싱을 위해 쿼리 워크로드를 희생하는 것은 바람직한 절충안이 아닙니다.

3. Azure Search 인덱서가 도달할 수 있는 수준의 여러 컨테이너에 데이터를 배포합니다. 여기에는 Azure SQL Database의 여러 테이블, Azure Blob 저장소의 여러 컨테이너 또는 여러 컬렉션이 해당될 수 있습니다. 각 테이블 또는 컨테이너에 대해 하나의 데이터 원본 개체를 정의합니다.

4. 병렬로 실행할 여러 인덱서 생성 및 예약:

   + 서비스에 복제본이 여섯 개 있다고 가정합니다. 전체 데이터 집합의 6방향 분할을 위해 데이터 집합의 1/6을 포함하는 데이터 원본에 각각 매핑된 인덱서 여섯 개를 구성합니다. 

   + 각 인덱서에서 동일한 인덱스를 가리킵니다. Cognitive Search 워크로드의 경우 각 인덱서가 동일한 기술 집합을 가리키도록 합니다.

   + 각 인덱서 정의 내에 동일한 런타임 실행 패턴을 예약합니다. 예를 들어, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`는 2018-05-15에 모든 인덱서에 대해 8시간 간격으로 실행되는 일정을 만듭니다.

예약된 시간에 모든 인덱서는 실행, 데이터 로드, 보강(Cognitive Search 파이프라인을 구성한 경우) 적용, 인덱스에 쓰기 작업을 시작합니다. Azure Search는 업데이트 시 인덱스를 잠그지 않습니다. 동시 쓰기가 관리되며 첫 번째 시도에서 특정 쓰기가 성공하지 못하면 다시 시도됩니다.

> [!Note]
> 복제본을 늘릴 때 인덱스 크기가 상당히 증가할 것으로 예상되면 파티션 수를 늘리는 것이 좋습니다. 파티션은 인덱싱된 콘텐츠 조각을 저장합니다. 파티션이 많을수록 각 파티션이 저장해야 하는 조각이 줄어듭니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Search의 보안](search-security-overview.md)