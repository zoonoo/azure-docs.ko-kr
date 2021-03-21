---
title: 인덱서 개요
titleSuffix: Azure Cognitive Search
description: 검색 가능한 데이터를 추출 하 고 Azure Cognitive Search 인덱스를 채우도록 Azure SQL Database, SQL Managed Instance, Azure Cosmos DB 또는 Azure storage를 탐색 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100098098"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search의 인덱서

Azure Cognitive Search의 *인덱서* 는 외부 Azure 데이터 원본에서 검색 가능한 텍스트 및 메타 데이터를 추출 하 고 원본 데이터와 인덱스 간의 필드-필드 매핑을 사용 하 여 검색 인덱스를 채우는 크롤러입니다. 이 접근 방식은 인덱스에 데이터를 추가 하는 코드를 작성 하지 않고도 서비스에서 데이터를 가져오기 때문에 ' 끌어오기 모델 '이 라고도 합니다. 또한 인덱서는 Cognitive Search의 [AI 보강](cognitive-search-concept-intro.md) 기능을 구동 하 여 경로에 있는 콘텐츠의 외부 처리를 인덱스로 통합 합니다.

인덱서는 azure [SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [azure Table Storage](search-howto-indexing-azure-tables.md) 및 [Blob Storage](search-howto-indexing-azure-blob-storage.md)에 대 한 개별 인덱서를 사용 하는 azure 전용입니다. 인덱서를 구성할 때 인덱스 (대상) 뿐만 아니라 데이터 원본 (원본)을 지정 합니다. Blob 저장소와 같은 여러 원본에는 해당 콘텐츠 형식에 특정 한 추가 구성 속성이 있습니다.

요청 시 또는 5 분 간격으로 자주 실행 되는 반복 되는 데이터 새로 고침 일정에서 인덱서를 실행할 수 있습니다. 업데이트를 자주 수행 하려면 Azure Cognitive Search와 외부 데이터 원본 모두에서 데이터를 동시에 업데이트 하는 [' 푸시 모델 '](search-what-is-data-import.md) 이 필요 합니다.

## <a name="usage-scenarios"></a>사용 시나리오

인덱서를 데이터 수집에 대 한 유일한 수단으로 사용 하거나, 필요에 따라 콘텐츠를 로드 하 고 선택적으로 변환 또는 보강 하는 기술 조합의 일부로 사용할 수 있습니다. 다음 표에는 주요 시나리오가 요약 되어 있습니다.

| 시나리오 |전략 |
|----------|---------|
| 단일 데이터 원본 | 이 패턴은 가장 간단한 방법입니다. 하나의 데이터 원본이 검색 인덱스에 대 한 유일한 콘텐츠 공급자입니다. 원본에서 검색 인덱스의 문서 키로 사용할 고유 값을 포함 하는 하나의 필드를 식별 합니다. 고유 값은 식별자로 사용 됩니다. 다른 모든 원본 필드는 암시적으로 또는 명시적으로 인덱스의 해당 필드에 매핑됩니다. </br></br>중요 한 요점은은 문서 키의 값이 원본 데이터에서 발생 한다는 것입니다. 검색 서비스는 키 값을 생성 하지 않습니다. 후속 실행에서 새 키가 있는 들어오는 문서는 추가 되지만, 기존 키가 있는 들어오는 문서는 인덱스 필드가 null 인지 아니면 채워져 있는지에 따라 병합 되거나 덮어쓰여집니다. |
| 여러 데이터 원본 | 인덱스는 여러 소스의 콘텐츠를 수락할 수 있습니다. 여기서 각 실행은 다른 소스에서 새 콘텐츠를 가져옵니다. </br></br>한 가지 결과는 각 인덱서가 실행 된 후 각 소스에서 전체 문서가 완전히 생성 된 후 문서를 얻는 인덱스 일 수 있습니다. 예를 들어 문서 1-100는 Blob storage에서, 문서 101-200는 Azure SQL에서 비롯 됩니다. 이 시나리오의 과제는 들어오는 모든 데이터에 대해 작동 하는 인덱스 스키마와 검색 인덱스에 있는 문서 키 구조를 디자인 하는 것입니다. 기본적으로 문서를 고유 하 게 식별 하는 값은 blob 컨테이너 및 SQL 테이블의 기본 키에 metadata_storage_path 됩니다. 콘텐츠 원본에 관계 없이 공통 형식으로 키 값을 제공 하도록 하나 또는 두 가지 소스를 수정 해야 한다고 가정할 수 있습니다. 이 시나리오에서는 단일 인덱스로 끌어올 수 있도록 데이터를 homogenize 하기 위해 일정 수준의 사전 처리를 수행 해야 합니다. </br></br>다른 결과는 첫 번째 실행에서 부분적으로 채워지는 검색 문서이 고 다른 소스에서 값을 가져오기 위해 후속 실행에 의해 추가로 채워질 수 있습니다. 예를 들어, 1-10 필드는 Blob storage, 11-20, Azure SQL 등에서 가져온 것입니다. 이 패턴의 과제는 각 인덱싱 실행이 동일한 문서를 대상으로 하는지 확인 하는 것입니다. 필드를 기존 문서에 병합 하려면 문서 키와 일치 해야 합니다. 이 시나리오에 대 한 데모를 보려면 [자습서: 여러 데이터 소스의 인덱스](tutorial-multiple-data-sources.md)를 참조 하십시오. |
| 여러 인덱서 | 여러 데이터 원본을 사용 하는 경우에는 런타임 매개 변수, 일정 또는 필드 매핑을 변경 해야 하는 경우 여러 인덱서가 필요할 수도 있습니다. 여러 인덱서-데이터 원본 집합은 동일한 인덱스를 대상으로 할 수 있지만 인덱스의 기존 값을 덮어쓸 수 있는 인덱서 실행에 주의 해야 합니다. 두 번째 인덱서와 데이터 소스가 동일한 문서 및 필드를 대상으로 하는 경우 첫 번째 실행의 모든 값을 덮어씁니다. 필드 값이 전체로 바뀝니다. 인덱서는 여러 실행의 값을 동일한 필드로 병합할 수 없습니다.</br></br>또 다른 다중 인덱서 사용 사례는 [지역 간 확장 Cognitive Search](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services)입니다. 서로 다른 지역에 동일한 검색 인덱스의 복사본이 있을 수 있습니다. 검색 인덱스 콘텐츠를 동기화 하기 위해 동일한 데이터 원본에서 여러 개의 인덱서를 끌어올 수 있습니다. 여기서 각 인덱서는 서로 다른 검색 인덱스를 대상으로 합니다.</br></br>매우 큰 데이터 집합의 [병렬 인덱싱에서](search-howto-large-index.md#parallel-indexing) 다중 인덱서 전략이 필요 합니다. 각 인덱서는 데이터의 하위 집합을 대상으로 합니다. |
| 콘텐츠 변환 | Cognitive Search는 이미지 분석과 자연어 처리를 추가 하 여 검색 가능한 새 콘텐츠와 구조를 만드는 선택적 [AI 보강](cognitive-search-concept-intro.md) 동작을 지원 합니다. AI 보강은 연결 된 [기술](cognitive-search-working-with-skillsets.md)를 통해 인덱서 기반입니다. AI 보강을 수행 하기 위해 인덱서에 인덱스와 Azure 데이터 원본이 필요 하지만이 시나리오에서는 기술 처리를 인덱서 실행에 추가 합니다. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

인덱서는 Azure에서 데이터 저장소를 크롤링합니다.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (미리 보기)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure Virtual Machines의 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

클라이언트 앱에 대 한 Azure 가상 네트워크를 사용 하는 경우 표준 인터넷 연결 (공용) 또는 암호화 된 개인 연결을 사용 하 여 원격 데이터 원본에 대 한 인덱서 연결을 만들 수 있습니다. 또한 신뢰할 수 있는 서비스 id를 사용 하 여 인증 하도록 연결을 설정할 수 있습니다. 보안 연결에 대 한 자세한 내용은 [개인 끝점을 통해 액세스 권한을 부여](search-indexer-securing-resources.md#granting-access-via-private-endpoints) 하 고 [관리 되는 id를 사용 하 여 데이터 원본에 연결](search-howto-managed-identities-data-sources.md)을 참조 하세요.

## <a name="stages-of-indexing"></a>인덱싱 단계

초기 실행 시 인덱스가 비어 있으면 테이블이 나 컨테이너에 제공 된 모든 데이터에서 인덱서가 읽혀집니다. 후속 실행에서 인덱서는 일반적으로 변경 된 데이터만 검색 하 고 검색할 수 있습니다. Blob 데이터의 경우 변경 검색은 자동으로 검색 됩니다. Azure SQL 또는 Cosmos DB 같은 다른 데이터 원본의 경우 변경 내용 검색을 사용 하도록 설정 해야 합니다.

인덱서는 수신 하는 각 문서에 대해 인덱싱에 대해 문서 검색에서 최종 검색 엔진 "전달"까지 여러 단계를 구현 하거나 조정 합니다. 필요에 따라 기술 실행 및 출력을 구동 하는 경우에도 인덱서가 지정 됩니다. 기술가 정의 되어 있다고 가정 합니다.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="인덱서 단계" border="false":::

### <a name="stage-1-document-cracking"></a>1 단계: 문서 크랙

문서 크랙은 파일을 열고 콘텐츠를 추출 하는 프로세스입니다. 데이터 원본 유형에 따라 인덱서는 잠재적으로 인덱싱 가능한 콘텐츠를 추출 하기 위해 다른 작업을 수행 합니다.  

예제:  

+ 문서가 [AZURE SQL 데이터 원본의](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)레코드인 경우 인덱서는 레코드에 대 한 각 필드를 추출 합니다.
+ 문서가 [Azure Blob Storage 데이터 원본의](search-howto-indexing-azure-blob-storage.md)PDF 파일이 면 인덱서가 텍스트, 이미지 및 메타 데이터를 추출 합니다.
+ 문서가 [Cosmos DB 데이터 원본의](search-howto-index-cosmosdb.md)레코드인 경우 인덱서는 Cosmos DB 문서에서 필드 및 하위 필드를 추출 합니다.

### <a name="stage-2-field-mappings"></a>2 단계: 필드 매핑 

인덱서는 원본 필드에서 텍스트를 추출 하 여 인덱스나 정보 저장소의 대상 필드에 보냅니다. 필드 이름과 형식이 일치 하는 경우 경로는 명확 하지 않습니다. 그러나 출력에 다른 이름이 나 형식이 필요할 수 있으며,이 경우에 필드를 매핑하는 방법을 인덱서에 알려야 합니다. 

이 단계는 문서를 크랙 한 후, 변환 전에 인덱서가 소스 문서에서 읽을 때 발생 합니다. [필드 매핑을](search-indexer-field-mappings.md)정의할 때 원본 필드의 값은 수정 없이 대상 필드에 그대로 보내집니다. 

### <a name="stage-3-skillset-execution"></a>3 단계: 기술 실행

기술 실행은 기본 제공 또는 사용자 지정 AI 처리를 호출 하는 선택적 단계입니다. 원본 데이터가 이진 이미지인 경우에는 이미지 분석 형식의 OCR (광학 문자 인식)에 필요할 수도 있고 콘텐츠가 다른 언어로 되어 있는 경우 언어 번역이 필요할 수도 있습니다. 

변환이 무엇이 든, 기술 실행은 보강 발생 합니다. 인덱서가 파이프라인 인 경우 [기술](cognitive-search-defining-skillset.md) 를 "파이프라인 내의 파이프라인" 이라고 생각할 수 있습니다.

### <a name="stage-4-output-field-mappings"></a>4 단계: 출력 필드 매핑

기술를 포함 하는 경우에는 출력 필드 매핑을 포함 해야 할 가능성이 높습니다. 기술의 출력은 사실 *보강 문서* 라고 하는 정보의 트리입니다. 출력 필드 매핑을 사용 하 여 인덱스의 필드에 매핑할이 트리의 파트를 선택할 수 있습니다. [출력 필드 매핑을 정의](cognitive-search-output-field-mapping.md)하는 방법에 대해 알아봅니다.

필드 매핑은 데이터 원본의 축 자 값을 대상 필드에 연결 하는 반면 출력 필드 매핑은 보강 문서의 변환 된 값을 인덱스의 대상 필드에 연결 하는 방법을 인덱서에 알려 줍니다. 선택적인 것으로 간주 되는 필드 매핑과 달리, 인덱스에 상주해 야 하는 변형 된 콘텐츠에 대해서는 항상 출력 필드 매핑을 정의 해야 합니다.

다음 이미지는 문서 크랙, 필드 매핑, 기술 실행 및 출력 필드 매핑과 같은 인덱서 단계의 샘플 인덱서 [디버그 세션](cognitive-search-debug-session.md) 표현을 보여 줍니다.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="샘플 디버그 세션" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>기본 워크플로

인덱서는 데이터 원본에 고유한 기능을 제공할 수 있습니다. 이러한 점에서 인덱서 또는 데이터 원본 구성의 일부 측면은 인덱서 유형에 따라 달라집니다. 그러나 인덱서는 모두 동일한 기본 구성 및 요구 사항을 공유합니다. 인덱서 모두에 공통되는 단계는 아래에서 다룹니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

인덱서에는 연결 문자열 및 자격 증명을 제공 하는 *데이터 원본* 개체가 필요 합니다. [데이터 소스 만들기 (REST)](/rest/api/searchservice/create-data-source) 또는 [SearchIndexerDataSourceConnection 클래스](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) 를 호출 하 여 리소스를 만듭니다.

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기

인덱서는 데이터 수집과 관련된 몇 가지 작업을 자동화하지만 인덱스를 만드는 작업은 일반적으로 포함되지 않습니다. 필수 구성 요소로서 외부 데이터 원본의 인덱스와 일치하는 필드를 포함한 미리 정의된 인덱스가 있어야 합니다. 필드는 이름 및 데이터 형식으로 일치 해야 합니다. 그렇지 않은 경우 [필드 매핑을 정의](search-indexer-field-mappings.md) 하 여 연결을 설정할 수 있습니다. 인덱스를 구조화 하는 방법에 대 한 자세한 내용은 [인덱스 만들기 (REST)](/rest/api/searchservice/Create-Index) 또는 [searchindex 클래스](/dotnet/api/azure.search.documents.indexes.models.searchindex)를 참조 하세요.

> [!Tip]
> 인덱서가 인덱스를 생성할 수 없지만 포털에서 **데이터 가져오기** 마법사를 통해 가능합니다. 대부분의 경우 마법사는 원본에서 기존 메타데이터의 인덱스 스키마를 유추할 수 있고 마법사가 활성화되어 있는 동안 인라인으로 편집할 수 있는 예비 인덱스 스키마를 표시합니다. 서비스에서 인덱스가 생성되면 포털에서 추가 편집 작업은 새 필드를 추가하는 것으로 제한됩니다. 인덱스를 수정하지 않고 만들기 위해 마법사를 사용합니다. 자동 학습은 [포털 연습](search-get-started-portal.md)의 단계를 수행합니다.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>3 단계: 인덱서 만들기 및 실행 (또는 예약)

검색 서비스에서 인덱서를 처음 [만들](/rest/api/searchservice/Create-Indexer) 때 인덱서가 실행 됩니다. 데이터 원본에 액세스할 수 있는지 또는 기술 유효한 지를 확인 하는 인덱서를 만들거나 실행 하는 경우에만 해당 합니다. 첫 번째 실행 후 [인덱서 실행](/rest/api/searchservice/run-indexer)을 사용 하 여 요청 시 다시 실행 하거나 [되풀이 일정을 정의할](search-howto-schedule-indexers.md)수 있습니다. 

포털에서 또는 [인덱서 상태 가져오기 API](/rest/api/searchservice/get-indexer-status)를 통해 인덱서 상태를 모니터링할 수 있습니다. 또한 [인덱스에 대해 쿼리를 실행](search-query-create.md) 하 여 결과가 예상한 결과 인지 확인 해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 소개 했으므로 다음 단계는 인덱서 속성과 매개 변수, 예약 및 인덱서 모니터링을 검토 하는 것입니다. 또는 특정 소스에 대 한 자세한 내용은 [지원 되는 데이터 원본](#supported-data-sources) 목록으로 돌아갈 수 있습니다.

+ [인덱서 만들기](search-howto-create-indexers.md)
+ [인덱서 다시 설정 및 실행](search-howto-run-reset-indexers.md)
+ [인덱서 일정](search-howto-schedule-indexers.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 상태 모니터링](search-howto-monitor-indexers.md)