---
title: Azure Search 인덱스 다시 빌드 또는 검색 가능한 콘텐츠 새로 고침 - Azure Search
description: 전체 다시 빌드 또는 부분 증분 인덱싱에서 새 요소를 추가하거나 기존 요소 또는 문서를 업데이트하거나 오래된 문서를 삭제하여 Azure Search 인덱스를 새로 고칩니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9c9af69e45af6a70c5327393a1c10385ba2c2aed
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316899"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Azure Search 인덱스를 다시 빌드하는 방법

인덱스를 다시 빌드하면 인덱스 구조가 변경되어 Azure Search 서비스의 인덱스에 대한 실제 표현이 변경됩니다. 이에 반해 인덱스 새로 고침은 콘텐트 전용 업데이트이며 영향을 주는 외부 데이터 원본에서 최신 변경 내용을 선택합니다. 이 문서에서는 구조적으로나 실질적으로 인덱스를 업데이트하는 방법에 대한 지침을 제공합니다.

인덱스 업데이트에는 서비스 수준의 읽기-쓰기 권한이 필요합니다. 프로그래밍 방식으로 REST 또는 .NET API를 호출하여 업데이트 옵션을 지정하는 매개 변수를 통해 콘텐츠 전체를 다시 작성하거나 증분 인덱싱을 수행할 수 있습니다. 

일반적으로 인덱스에 대한 업데이트는 주문형입니다. 하지만 특정 소스에 대한 [인덱서](search-indexer-overview.md)를 사용하여 채워진 인덱스의 경우 기본 제공 스케줄러를 사용할 수 있습니다. 스케줄러는 필요한 간격 및 패턴에 따라 최대 15분 간격으로 문서 새로 고침을 지원합니다. 새로 고침 빈도를 높이려면 외부 데이터 원본과 Azure Search 인덱스를 동시에 업데이트하여 트랜잭션에 대한 이중 쓰기를 통해 수동으로 인덱스 업데이트 내용을 밀어 넣어야 합니다.

## <a name="full-rebuilds"></a>전체 다시 빌드

많은 유형의 업데이트에 대해 전체 다시 빌드가 필요합니다. 전체 다시 빌드란 인덱스(데이터와 메타데이터 모두)를 삭제한 다음, 외부 데이터 원본으로 인덱스를 다시 채우는 작업을 말합니다. 프로그래밍 방식으로 인덱스를 [삭제](https://docs.microsoft.com/rest/api/searchservice/delete-index), [생성](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [다시 로드](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)하여 다시 빌드합니다. 

다시 빌드한 다음, 쿼리 패턴 및 채점 프로파일을 테스트해봤다면, 기본 콘텐츠가 변경된 경우 쿼리 결과의 변형을 예상할 수 있습니다.

## <a name="when-to-rebuild"></a>다시 빌드하는 경우

인덱스 스키마가 유동적인 상태인 경우 개발하는 동안 빈번한 전체 다시 빌드를 계획합니다.

| 수정 | 다시 빌드 상태|
|--------------|---------------|
| 필드 이름, 데이터 형식 또는 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index) 변경 | 필드 정의를 변경하면 일반적으로 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index): Retrievable, SearchAnalyzer, SynonymMaps를 제외하고 다시 빌드 패널티가 발생합니다. 색인을 다시 빌드할 필요 없이 Retrievable, SearchAnalyzer 및 SynonymMaps 특성을 기존 필드에 추가할 수 있습니다.|
| 필드 추가 | 다시 빌드에 대한 엄격한 요구 사항은 없습니다. 기존의 인덱싱된 문서에는 새 필드에 null 값이 제공됩니다. 나중에 인덱스를 다시 수행하면 원본 데이터의 값이 Azure Search에 의해 추가된 null을 대체합니다. |
| 필드 삭제 | Azure Search 인덱스에서 필드를 직접 삭제할 수는 없습니다. 대신 응용 프로그램이 "삭제된" 필드를 무시하고 사용하지 않도록 해야 합니다. 물리적으로 필드 정의와 콘텐츠는 다음 번에 해당 필드를 생략하는 스키마를 사용하여 인덱스를 다시 빌드할 때까지 인덱스에 남아 있습니다.|

> [!Note]
> 계층을 전환하는 경우에도 다시 빌드가 필요합니다. 특정 시점에서 용량 확장을 결정하는 경우, 현재 위치 업그레이드가 없습니다. 새 용량 지점에서 새 서비스를 만들어야 하며 새 서비스에서 인덱스는 처음부터 빌드해야 합니다. 

## <a name="partial-or-incremental-indexing"></a>부분 또는 증분 인덱싱

인덱스가 프로덕션 환경에 있으면 증분 인덱싱에 중점을 두게 되며 인식할만한 서비스 중단은 대개 없습니다. 부분 인덱싱 또는 증분 인덱싱은 검색 인덱스의 콘텐츠를 동기화하여 영향을 주는 데이터 원본의 콘텐트 상태를 반영하는 콘텐트 전용 워크로드입니다. 원본에 추가되거나 삭제된 문서가 인덱스에 추가되거나 삭제됩니다. 코드에서 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 작업이나 .NET의 해당 작업을 호출합니다.

> [!Note]
> 외부 데이터 원본을 크롤링하는 인덱서를 사용하는 경우 원본 시스템의 변경 내용 추적 메커니즘을 사용하여 증분 인덱싱이 수행됩니다. [Azure Blob 저장소](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)의 경우 `lastModified` 필드가 사용됩니다. [Azure Table 저장소](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)에서 `timestamp`는 동일한 용도로 사용됩니다. 마찬가지로 [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)와 [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md#indexing-changed-documents) 모두에는 행 업데이트 플래그를 지정하는 필드가 있습니다. 인덱서에 대한 자세한 내용은 [인덱서 개요](search-indexer-overview.md)를 참조하세요.


## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [대규모 데이터 집합 인덱싱](search-howto-large-index.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Search의 보안](search-security-overview.md)