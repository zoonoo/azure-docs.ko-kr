---
title: 가져오는 동안 데이터를 크롤링하기 위한 인덱서
titleSuffix: Azure Cognitive Search
description: Azure SQL 데이터베이스, Azure Cosmos DB 또는 Azure 저장소를 크롤링하여 검색 가능한 데이터를 추출하고 Azure 인지 검색 인덱스를 채웁니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282992"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search의 인덱서

Azure Cognitive Search의 *인덱서는* 외부 Azure 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑을 기반으로 인덱스를 채우는 크롤러입니다. 이 방법을 '끌어오기 모델'이라고도 하는데, 이는 인덱스에 데이터를 추가하는 코드를 작성할 필요 없이 서비스가 데이터를 가져오기 때문입니다.

인덱서에는 Azure, Cosmos DB, Azure 테이블 저장소 및 Blob 저장소의 SQL Server용 개별 인덱서가 있는 데이터 원본 형식 또는 플랫폼을 기반으로 합니다. Blob 저장소 인덱스터에는 Blob 콘텐츠 형식과 관련된 추가 속성이 있습니다.

데이터 수집을 위한 유일한 방법으로 인덱서를 사용하거나, 인덱서 사용이 포함된 기술의 조합을 통해 인덱스의 일부 필드만 로드할 수 있습니다.

요청 시 또는 5분마다 자주 실행되는 되풀이 데이터 새로 고침 일정에 따라 인덱서를 실행할 수 있습니다. 업데이트 빈도가 높기 때문에 Azure Cognitive Search와 외부 데이터 원본 모두에서 데이터를 동시에 업데이트하는 푸시 모델이 필요합니다.

## <a name="approaches-for-creating-and-managing-indexers"></a>인덱서를 만들고 관리하는 접근 방식

이러한 접근 방식을 사용하여 인덱서를 만들고 관리할 수 있습니다.

* [데이터 가져오기 마법사를 > 포털](search-import-data-portal.md)
* [서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

처음에 새 인덱서는 미리 보기 기능으로 발표됩니다. 미리 보기 기능은 API(REST 및 .NET)에 도입된 다음 일반 공급으로 조정 후 포털로 통합됩니다. 새 인덱서를 평가하고 있다면 코드 작성을 계획해야 합니다.

## <a name="permissions"></a>사용 권한

상태 또는 정의에 대한 GET 요청을 포함하여 인덱서와 관련된 모든 작업에는 [관리자 API 키가](search-security-api-keys.md)필요합니다. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

인덱서는 Azure에서 데이터 저장소를 크롤링합니다.

* [Azure Blob 저장소](search-howto-indexing-azure-blob-storage.md)
* [Azure 데이터 레이크 스토리지 Gen2(미리](search-howto-index-azure-data-lake-storage.md) 보기)
* [Azure 테이블 저장소](search-howto-indexing-azure-tables.md)
* [Azure 코스모스 DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Virtual Machines의 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Azure에서 SQL 관리 인스턴스](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>기본 구성 단계
인덱서는 데이터 원본에 고유한 기능을 제공할 수 있습니다. 이러한 점에서 인덱서 또는 데이터 원본 구성의 일부 측면은 인덱서 유형에 따라 달라집니다. 그러나 인덱서는 모두 동일한 기본 구성 및 요구 사항을 공유합니다. 인덱서 모두에 공통되는 단계는 아래에서 다룹니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기
인덱서가 데이터 원본 개체에서 *데이터 원본* 연결을 가져옵니다. 데이터 원본 정의는 연결 문자열 및 자격 증명을 제공합니다. [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API 또는 [DataSource 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)를 호출하여 리소스를 만듭니다.

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기
인덱서는 데이터 수집과 관련된 몇 가지 작업을 자동화하지만 인덱스를 만드는 작업은 일반적으로 포함되지 않습니다. 필수 구성 요소로서 외부 데이터 원본의 인덱스와 일치하는 필드를 포함한 미리 정의된 인덱스가 있어야 합니다. 필드는 이름과 데이터 유형으로 일치해야 합니다. 인덱스 구조화에 대한 자세한 내용은 [인덱스 만들기(Azure 인지 검색 REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) 또는 [인덱스 클래스를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)참조하십시오. 필드 연결에 대한 도움말은 [Azure 인지 검색 인덱서의 필드 매핑을](search-indexer-field-mappings.md)참조하십시오.

> [!Tip]
> 인덱서가 인덱스를 생성할 수 없지만 포털에서 **데이터 가져오기** 마법사를 통해 가능합니다. 대부분의 경우 마법사는 원본에서 기존 메타데이터의 인덱스 스키마를 유추할 수 있고 마법사가 활성화되어 있는 동안 인라인으로 편집할 수 있는 예비 인덱스 스키마를 표시합니다. 서비스에서 인덱스가 생성되면 포털에서 추가 편집 작업은 새 필드를 추가하는 것으로 제한됩니다. 인덱스를 수정하지 않고 만들기 위해 마법사를 사용합니다. 자동 학습은 [포털 연습](search-get-started-portal.md)의 단계를 수행합니다.

### <a name="step-3-create-and-schedule-the-indexer"></a>3단계: 인덱서 만들기 및 예약
인덱서 정의는 데이터 수집과 관련된 모든 요소를 함께 제공하는 구문입니다. 필수 요소에는 데이터 원본 및 인덱스가 포함됩니다. 선택적 요소에는 일정 및 필드 매핑이 포함됩니다. 필드 매핑은 소스 필드와 인덱스 필드가 명확하게 일치하는 경우에만 선택 사항입니다. 인덱서 구조화에 대한 자세한 내용은 [인덱서 만들기(Azure 인지 검색 REST API)를](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)참조하십시오.

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>온디맨드 인덱서 실행

인덱싱을 예약하는 것이 일반적이지만 [Run 명령을](https://docs.microsoft.com/rest/api/searchservice/run-indexer)사용하여 요청 시 인덱서를 호출할 수도 있습니다.

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> API 실행이 성공적으로 반환되면 인덱서 호출이 예약된 것이지만 실제 처리는 비동기적으로 발생합니다. 

포털 또는 인덱서 상태 API 를 통해 인덱서 상태를 모니터링할 수 있습니다. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>인덱서 상태 얻기

인덱서 상태 받기 명령을 통해 인덱서의 상태 및 실행 기록을 검색할 수 [있습니다.](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

응답에는 전반적인 인덱서 상태, 마지막(또는 진행 중인) 인덱서 호출 및 최근 인덱서 호출 기록이 포함됩니다.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)

## <a name="next-steps"></a>다음 단계
이제 기본 개념을 이해했으므로 다음 단계는 각 데이터 원본 유형과 관련된 요구 사항 및 작업을 검토하는 것입니다.

* [Azure SQL Database 또는 Azure 가상 머신의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure 코스모스 DB](search-howto-index-cosmosdb.md)
* [Azure Blob 저장소](search-howto-indexing-azure-blob-storage.md)
* [Azure 테이블 저장소](search-howto-indexing-azure-tables.md)
* [Azure 인지 검색 Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
* [Azure 인지 검색 Blob 인덱서를 사용하여 JSON Blob 인덱싱](search-howto-index-json-blobs.md)
