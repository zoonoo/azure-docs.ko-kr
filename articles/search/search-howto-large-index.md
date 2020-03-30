---
title: 내장 인덱서를 사용하여 대용량 데이터 세트 인덱싱
titleSuffix: Azure Cognitive Search
description: 대량 데이터 인덱싱 또는 배치 모드, 리소스 및 예약, 병렬 및 분산 인덱싱을 위한 기술을 통한 계산 집약적인 인덱싱을 위한 전략
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190955"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Azure 인지 검색에서 대용량 데이터 집합을 인덱싱하는 방법

데이터 볼륨이 증가하거나 처리 요구 사항이 변경됨에 따라 단순 또는 기본 인덱싱 전략이 더 이상 실용적이지 않을 수 있습니다. Azure Cognitive Search의 경우 데이터 업로드 요청을 구성하는 방법부터 예약된 워크로드 및 분산 워크로드에 대한 원본 별 인덱서 사용에 이르기까지 더 큰 데이터 집합을 수용하기 위한 몇 가지 방법이 있습니다.

장기 실행 프로세스에도 동일한 기술이 적용됩니다. 특히 [병렬 인덱싱에](#parallel-indexing) 설명된 단계는 [AI 보강 파이프라인에서](cognitive-search-concept-intro.md)이미지 분석 또는 자연어 처리와 같은 계산 집약적인 인덱싱에 유용합니다.

다음 섹션에서는 대용량 데이터를 인덱싱하기 위한 세 가지 기술을 살펴봅니다.

## <a name="option-1-pass-multiple-documents"></a>옵션 1: 여러 문서 전달

대용량 데이터 세트를 인덱싱하기 위한 가장 간단한 메커니즘 중 하나는 단일 요청에서 여러 문서 또는 레코드를 제출하는 것입니다. 전체 페이로드가 16MB 미만인 한 요청은 대량 업로드 작업에서 최대 1000개의 문서를 처리할 수 있습니다. 이러한 제한은 .NET SDK에서 [문서 REST 추가 API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 또는 인덱스 [메서드를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) 사용 하든 관계없이 적용됩니다. 두 API의 경우 각 요청의 본문에 1000개의 문서를 패키징합니다.

일괄 처리 인덱싱은 REST 또는 .NET을 사용하여 또는 인덱서를 통해 개별 요청에 대해 구현됩니다. 몇 가지 인덱서는 다른 제한에서 작동합니다. 특히 Azure Blob 인덱싱은 큰 평균 문서 크기의 인식으로 10개의 문서에서 일괄 처리 크기를 설정합니다. [만들기 인덱서 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)에 따른 인덱서의 경우 `BatchSize` 인수를 설정하여 데이터의 특징에 더 잘 일치하도록 이 설정을 사용자 지정할 수 있습니다. 

> [!NOTE]
> 문서 크기를 줄이려면 쿼리할 수 없는 데이터를 인덱스에 추가하지 마십시오. 이미지 및 기타 이진 데이터는 직접 검색할 수 없으므로 인덱스에 저장하지 않아야 합니다. 쿼리할 수 없는 데이터를 검색 결과에 통합하려면 리소스에 대한 URL 참조를 저장하는 검색할 수 없는 필드를 정의해야 합니다.

## <a name="option-2-add-resources"></a>옵션 2: 리소스 추가

[표준 가격 책정 계층](search-sku-tier.md) 중 하나에서 프로비전되는 서비스는 종종 스토리지 및 워크로드(쿼리 또는 인덱싱) 모두에 대해 활용도 낮은 용량을 갖습니다. 이는 [파티션 및 복제본 수를 늘려](search-capacity-planning.md) 더 큰 데이터 세트를 수용하기 위한 확실한 솔루션이 됩니다. 최상의 결과를 위해 스토리지에 대한 파티션 및 데이터 수집 작업에 대한 복제본의 리소스가 필요합니다.

복제본과 파티션을 늘리는 것은 비용을 증가시키는 청구 가능한 이벤트이지만 최대 부하에서 지속적으로 인덱싱하지 않는 한 인덱싱 프로세스 기간 동안 배율을 추가한 다음 인덱싱 후 리소스 수준을 다시 아래쪽으로 조정할 수 있습니다. 완료.

## <a name="option-3-use-indexers"></a>옵션 3: 인덱서 사용

[인덱서를](search-indexer-overview.md) 사용하여 검색 가능한 콘텐츠에 대해 지원되는 Azure 데이터 원본을 크롤링합니다. 대규모 인덱싱에 대해 명시적으로 의도하지 않으므로 여러 인덱서 기능은 더 큰 데이터 세트를 수용하는 데 특히 유용합니다.

+ 스케줄러를 통해 시간이 지남에 따라 분배할 수 있도록 정기적으로 인덱싱을 분배할 수 있습니다.
+ 예약된 인덱싱은 마지막으로 알려진 중지 지점에서 다시 시작할 수 있습니다. 데이터 원본이 24시간 기간 내에 완벽하게 크롤링되지 않는 경우 인덱서는 중단 위치에서 두 번째 날에 인덱싱을 다시 시작합니다.
+ 데이터를 작은 개별 데이터 원본으로 분할하면 병렬 처리가 가능합니다. 원본 데이터를 Azure Blob 저장소의 여러 컨테이너로 같은 더 작은 구성 요소로 나간 다음 병렬로 인덱싱할 수 있는 Azure Cognitive Search에서 해당 여러 [데이터 원본 개체를](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 만들 수 있습니다.

> [!NOTE]
> 인덱서는 데이터 원본에 따라 다르므로 인덱서 접근 방식을 사용하는 것은 Azure에서 선택한 데이터 원본에 대해서만 실행 가능합니다: [SQL Database,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Blob 저장소,](search-howto-indexing-azure-blob-storage.md) [테이블 저장소,](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>예약된 인덱싱

인덱서 예약은 큰 데이터 세트를 처리하고 인식 검색 파이프라인에서 이미지 분석과 같이 느리게 처리되는 분석을 처리하는 중요한 메커니즘입니다. 인덱서 처리는 24시간 범위 내에 실행됩니다. 24시간 내에 처리가 완료되지 않으면 인덱서 일정 예약의 동작이 사용자에게 유리하게 작용할 수 있습니다. 

기본적으로 예약된 인덱싱은 특정 간격으로 시작되며, 대개 다음으로 예약된 간격에서 다시 시작되기 전에 작업이 완료됩니다. 하지만 간격 내에 처리가 완료되지 않으면 인덱서가 중지됩니다(시간이 초과되기 때문임). 다음 번 간격이 시작되면, 마지막으로 중단된 위치에서 처리가 다시 시작되며, 시스템은 해당 위치를 추적합니다. 

실제 여러 날에 걸쳐 수행되는 인덱스 로드의 경우 24시간 일정으로 인덱서를 배치할 수 있습니다. 다음 번 24시간 주기에서 인덱싱이 다시 시작되면 마지막으로 성공한 문서에서 다시 시작됩니다. 이러한 방식으로 인덱서는 처리되지 않은 모든 문서가 처리될 때까지 여러 날에 걸쳐 문서 백로그를 통해 작업을 수행할 수 있습니다. 이러한 방식에 대한 자세한 내용은 [Azure Blob 스토리지에서 큰 데이터 세트 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)을 참조하세요. 일반적으로 일정 설정에 대한 자세한 내용은 [인덱서 REST API 만들기를](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) 참조하거나 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법을](search-howto-schedule-indexers.md)참조하십시오.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>병렬 인덱싱

병렬 인덱싱 전략은 여러 데이터 원본을 한 번에 인덱싱하는 데 기반하며 여기에서 각 데이터 원본 정의는 데이터의 하위 세트를 지정합니다. 

비 루틴, 계산 집약적 인덱싱 요구 사항의 경우(예제: 인식 검색 파이프라인에서 스캔된 문서의 OCR, 이미지 분석 또는 자연어 처리) 병렬 인덱싱 전략은 종종 짧은 시간에 장기 실행 프로세스를 완료하기 위한 적합한 방법입니다. 쿼리 요청을 제거하거나 줄일 수 있는 경우 쿼리를 동시에 처리하지 않는 서비스에 대한 병렬 인덱싱은 처리 속도가 느린 대량의 콘텐츠를 통한 작업에 최상의 전략 옵션입니다. 

병렬 처리에는 다음과 같은 요소가 있습니다.

+ 원본 데이터를 동일한 컨테이너 내의 여러 컨테이너 또는 여러 가상 폴더로 세분화합니다. 
+ 각 미니 데이터 집합을 자체 [데이터 원본에](https://docs.microsoft.com/rest/api/searchservice/create-data-source)매핑하고 해당 [인덱서에](https://docs.microsoft.com/rest/api/searchservice/create-indexer)페어링합니다.
+ Cognitive Search의 경우 각 인덱서 정의에 동일한 [기술 집합](https://docs.microsoft.com/rest/api/searchservice/create-skillset)을 참조합니다.
+ 동일한 대상 검색 인덱스에 기록합니다. 
+ 모든 인덱서가 동시에 실행되도록 예약합니다.

> [!NOTE]
> Azure Cognitive Search에서는 인덱싱 또는 쿼리 처리에 개별 복제본 또는 파티션을 할당할 수 없습니다. 시스템은 리소스사용 방법을 결정합니다. 쿼리 성능에 미치는 영향을 이해하려면 테스트 환경에서 병렬 인덱싱을 시도한 다음 프로덕션 환경에 롤링할 수 있습니다.  

### <a name="how-to-configure-parallel-indexing"></a>병렬 인덱싱을 구성하는 방법

인덱서의 경우 처리 용량은 검색 서비스에 사용되는 각 SU(서비스 단위)에 대해 하나의 인덱서 하위 시스템을 기반으로 합니다. 두 개 이상의 복제본이 있는 기본 또는 표준 계층에 프로비전된 Azure Cognitive Search 서비스에서 여러 동시 인덱서가 가능합니다. 

1. [Azure Portal](https://portal.azure.com)의 검색 서비스 대시보드 **개요** 페이지에서 **가격 책정 계층**을 확인하여 병렬 인덱싱을 수용할 수 있는지 확인합니다. 기본 및 표준 계층 모두 여러 복제본이 제공됩니다.

2. **설정** > **규모에서**병렬 처리를 위한 [복제본을 늘립니다:](search-capacity-planning.md) 각 인덱서 워크로드에 대해 하나의 추가 복제본. 기존 쿼리 볼륨에 대해 충분한 수를 유지합니다. 인덱싱을 위해 쿼리 워크로드를 희생하는 것은 바람직한 절충안이 아닙니다.

3. Azure 인지 검색 인덱서가 도달할 수 있는 수준에서 여러 컨테이너에 데이터를 배포합니다. 여기에는 Azure SQL Database의 여러 테이블, Azure Blob Storage의 여러 컨테이너 또는 여러 컬렉션이 해당될 수 있습니다. 각 테이블 또는 컨테이너에 대해 하나의 데이터 원본 개체를 정의합니다.

4. 병렬로 실행할 여러 인덱서 생성 및 예약:

   + 서비스에 복제본이 여섯 개 있다고 가정합니다. 전체 데이터 집합의 6방향 분할을 위해 데이터 집합의 1/6을 포함하는 데이터 원본에 각각 매핑된 인덱서 여섯 개를 구성합니다. 

   + 각 인덱서에서 동일한 인덱스를 가리킵니다. Cognitive Search 워크로드의 경우 각 인덱서가 동일한 기술 집합을 가리키도록 합니다.

   + 각 인덱서 정의 내에 동일한 런타임 실행 패턴을 예약합니다. 예를 들어, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`는 2018-05-15에 모든 인덱서에 대해 8시간 간격으로 실행되는 일정을 만듭니다.

예약된 시간에 모든 인덱서는 실행, 데이터 로드, 보강(Cognitive Search 파이프라인을 구성한 경우) 적용, 인덱스에 쓰기 작업을 시작합니다. Azure 인지 검색 업데이트에 대 한 인덱스를 잠그지 않습니다. 동시 쓰기가 관리되며 첫 번째 시도에서 특정 쓰기가 성공하지 못하면 다시 시도됩니다.

> [!Note]
> 복제본을 늘릴 때 인덱스 크기가 상당히 증가할 것으로 예상되면 파티션 수를 늘리는 것이 좋습니다. 파티션은 인덱싱된 콘텐츠 조각을 저장합니다. 파티션이 많을수록 각 파티션이 저장해야 하는 조각이 줄어듭니다.

## <a name="see-also"></a>참조

+ [인덱서 개요](search-indexer-overview.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob 저장소 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure 테이블 저장소 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure 인지 검색의 보안](search-security-overview.md)
