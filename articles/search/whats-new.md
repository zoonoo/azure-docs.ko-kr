---
title: Azure Cognitive Search의 새로운 기능
description: 서비스 이름을 Azure Search에서 Azure Cognitive Search로 변경하는 것을 포함하여 새로운 기능과 향상된 기능에 대한 공지 사항입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 078892691bfaec62f71f9d601a42de3f80221149
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958160"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지하려면 이 페이지에 대한 책갈피를 지정하세요.

## <a name="feature-announcements"></a>기능 공지

### <a name="june-2020"></a>2020년 6월

+ [지식 저장소](knowledge-store-concept-intro.md)는 현재 일반 공급됩니다.

+ [Search Service REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/)은 최신 버전의 안정적인 REST API입니다. 이 일반 공급 버전에는 지식 저장소 외에도 향상된 검색 관련성 및 채점 기능이 포함되어 되었습니다.

+ 새로운 관련성 순위 지정 알고리즘은 새로 만드는 모든 서비스에 대한 [BM25](https://en.wikipedia.org/wiki/Okapi_BM25)입니다. 기존 서비스의 경우 인덱스 필드의 `similarity` 속성을 설정하여 옵트인할 수 있습니다. 이 속성은 일반 공급됩니다.

+ 새 인덱서 `executionEnvironment`를 명시적으로 `private`으로 설정할 수 있습니다. 이 기능은 프라이빗 엔드포인트를 통해 외부 데이터에 대한 인덱서 액세스를 지원하며 일반 공급됩니다.

+ [AML(Azure Machine Learning)](cognitive-search-aml-skill.md) 기술은 Azure Machine Learning의 추론 엔드포인트를 통합하는 새로운 기술 유형입니다. 포털 환경에서는 Cognitive Search 기술 세트 내에서 Azure Machine Learning 엔드포인트의 검색 및 통합을 지원합니다. 검색을 수행하려면 Cognitive Search 및 Azure ML 서비스가 동일한 구독에 배포되어야 합니다. 이 기술은 일반 공급됩니다. [이 자습서](cognitive-search-tutorial-aml-custom-skill.md)를 시작합니다.

### <a name="may-2020-microsoft-build"></a>2020년 5월(Microsoft Build)

+ [디버그 세션](cognitive-search-debug-session.md) 기능은 현재 미리 보기로 제공됩니다. 디버그 세션은 기술 세트 관련 문제를 조사하고 해결할 수 있는 포털 기반 인터페이스를 제공합니다. 디버그 세션에서 만들어진 픽스는 프로덕션 기술 세트에 저장할 수 있습니다. [이 자습서](cognitive-search-tutorial-debug-sessions.md)를 시작합니다.

+ [인바운드 방화벽 지원을 위한 IP 규칙을 구성](service-configure-firewall.md)하거나 [프라이빗 검색 엔드포인트에 Azure Private Link](service-create-private-endpoint.md)를 활용하여 공용 인터넷에서 검색 서비스 엔드포인트를 보호합니다. 두 기능 모두 일반 공급됩니다.

+ [시스템 관리 ID(미리 보기)](search-howto-managed-identities-data-sources.md)를 사용하여 인덱싱을 위해 Azure 데이터 원본에 대한 연결을 설정합니다. Azure SQL Database, Azure Cosmos DB 및 Azure Storage와 같은 Azure 데이터 원본에서 콘텐츠를 수집하는 [인덱서](search-indexer-overview.md)에 적용됩니다.

+ [sessionId](index-similarity-and-scoring.md) 및 [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics) 쿼리 매개 변수를 사용하여 검색 점수가 계산되는 기본 방식을 분할 단위에서 전체 분할로 변경합니다. 이러한 매개 변수는 일반 공급됩니다.

+ [featuresMode(미리 보기)](index-similarity-and-scoring.md#featuresMode-param) 쿼리 매개 변수를 추가하여 더 많은 세부 정보(필드별 유사성 점수, 필드별 용어 빈도, 필드별 일치하는 고유 토큰 수)를 표시하도록 관련성 점수를 확장합니다. 이러한 데이터 요소를 사용자 지정 채점 알고리즘에 사용할 수 있습니다. 이 기능을 보여주는 샘플은 [검색 관련성에 기계 학습(LearnToRank) 추가](https://github.com/Azure-Samples/search-ranking-tutorial)를 참조하세요.

### <a name="march-2020"></a>2020년 3월

+ [네이티브 Blob 일시 삭제(미리 보기)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)는 Azure Cognitive Search의 Azure Blob Storage 인덱서가 일시 삭제된 상태에 있는 Blob을 인식하고 인덱싱 중에 해당 검색 문서를 제거함을 의미합니다.

+ 최신 버전의 안정적인 [관리 REST API(2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions)가 일반 공급됩니다. 

### <a name="february-2020"></a>2020년 2월

+ [PII 검색(미리 보기)](cognitive-search-skill-pii-detection.md)는 인덱싱 중에 사용되는 인지 기술로서, 입력 텍스트에서 개인 식별이 가능한 정보를 추출하여 다양한 방법으로 해당 텍스트에서 마스킹할 수 있는 옵션을 제공합니다.

+ [사용자 지정 엔터티 조회(미리 보기)](cognitive-search-skill-custom-entity-lookup.md )는 사용자 지정된 단어 및 구의 사용자 정의 목록에서 텍스트를 찾습니다. 이 목록을 사용하면 일치하는 엔터티가 있는 모든 문서에 레이블이 지정됩니다. 또한 이 기술은 비슷하지만 정확하지 않는 일치 항목을 찾는 데 적용할 수 있는 유사 항목 일치 수준을 지원합니다. 

### <a name="january-2020"></a>2020년 1월

+ [고객이 관리하는 암호화 키](search-security-manage-encryption-keys.md)는 이제 일반 공급됩니다. REST를 사용하는 경우 `api-version=2019-05-06` 이상을 사용하여 기능에 액세스할 수 있습니다. 관리 코드의 경우 기능이 미리 보기 범위를 벗어나더라도 올바른 패키지는 여전히 [.NET SDK 버전 8.0-preview](search-dotnet-sdk-migration-version-9.md)입니다. 

+ 검색 서비스에 대한 프라이빗 액세스는 현재 미리 보기에 있는 두 가지 메커니즘을 통해 제공됩니다.

  + `api-version=2019-10-01-Preview` 관리 REST API를 사용하여 서비스를 만들면 특정 IP 주소에 대한 액세스를 제한할 수 있습니다. 미리 보기 API의 [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service)에는 새로운 **IpRule** 및 **NetworkRuleSet** 속성이 있습니다. 이 미리 보기 기능은 선택한 지역에서 사용할 수 있습니다. 자세한 내용은 [관리 REST API를 사용하는 방법](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)을 참조하세요.

### <a name="december-2019"></a>2019년 12월

+ [데모 앱 만들기(미리 보기)](search-create-app-portal.md)는 인덱스에 대한 쿼리(읽기 전용) 액세스 권한을 사용하여 다운로드 가능한 HTML 파일을 생성하는 포털의 새 마법사입니다. 이 파일에는 검색 서비스의 인덱스에 바인딩된 작동 가능한 "localhost" 스타일의 웹앱을 렌더링하는 포함 스크립트가 포함되어 있습니다. 페이지는 마법사에서 구성할 수 있으며 검색 창, 결과 영역, 사이드바 탐색 및 자동 완성 쿼리 지원을 포함할 수 있습니다. 워크플로 또는 모양을 확장하거나 사용자 지정하기 위해 HTML을 오프라인에서 수정할 수 있습니다. 데모 앱은 일반적으로 프로덕션 시나리오에 필요한 보안 및 호스팅 계층을 포함하도록 확장하기가 쉽지 않습니다. 완전한 클라이언트 앱을 만들기 위한 지름길이 아니라 간단한 유효성 검사 및 테스트 도구로 생각해야 합니다.

+ [보안 연결을 위한 프라이빗 엔드포인트 만들기(미리 보기)](service-create-private-endpoint.md)는 검색 서비스에 대한 보안 연결을 위한 Private Link를 설정하는 방법에 대해 설명합니다. 이 미리 보기 기능은 요청 시 사용할 수 있으며, [Azure Private Link](../private-link/private-link-overview.md) 및 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)를 솔루션의 일부로 사용합니다.

### <a name="november-2019---ignite-conference"></a>2019년 11월 - Ignite 컨퍼런스

+ [증분 보강(미리 보기)](cognitive-search-incremental-indexing-conceptual.md)은 이미 처리된 콘텐츠를 손실하지 않고 특정 단계에서 작업할 수 있도록 캐싱 및 상태 저장 가능성을 보강 파이프라인에 추가합니다. 이전에는 보강 파이프라인을 변경하기 위해 전체적으로 다시 빌드해야 했습니다. 비용이 많이 드는 분석, 특히 이미지 분석의 출력은 증분 보강을 통해 유지됩니다.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [문서 추출(미리 보기)](cognitive-search-skill-document-extraction.md)은 인덱싱 중에 사용되는 인지 기술로서, 기술 세트 내에서 파일의 내용을 추출할 수 있습니다. 이전에는 문서 크래킹이 기술 세트를 실행하기 전에만 발생했습니다. 이 기술이 추가되면 이 작업을 기술 세트 실행 내에서 수행할 수도 있습니다.

+ [텍스트 번역](cognitive-search-skill-text-translation.md)은 인덱싱 중에 사용되는 인지 기술로서, 각 레코드에 대해 지정된 대상 언어로 변환된 텍스트를 반환합니다.

+ [Power BI 템플릿](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)은 Power BI Desktop의 지식 저장소에서 보강 콘텐츠를 빠르게 시각화하고 분석할 수 있습니다. 이 템플릿은 [데이터 가져오기 마법사](knowledge-store-create-portal.md)를 통해 만든 Azure 테이블 프로젝션용으로 설계되었습니다.

+ [Azure Data Lake Storage Gen2(미리 보기)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API(미리 보기)](search-howto-index-cosmosdb.md) 및 [Cosmos DB Cassandra API(미리 보기)](search-howto-index-cosmosdb.md)는 이제 인덱서에서 지원됩니다. [이 양식](https://aka.ms/azure-cognitive-search/indexer-preview)을 사용하여 가입할 수 있습니다. 미리 보기 프로그램에 동의하면 확인 이메일을 받게 됩니다.

### <a name="july-2019"></a>2019년 7월

+ [Azure Government 클라우드](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)에서 일반적으로 사용할 수 있습니다.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>새 서비스 이름

핵심 작업에서 확장된 인지 기술과 AI 처리(아직 선택 사항)를 사용할 수 있도록 Azure Search의 이름이 이제 **Azure Cognitive Search**로 변경되었습니다. API 버전, NuGet 패키지, 네임스페이스 및 엔드포인트는 변경되지 않았습니다. 새 검색 솔루션과 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="service-updates"></a>서비스 업데이트

Azure Cognitive Search에 대한 [서비스 업데이트 공지](https://azure.microsoft.com/updates/?product=search&status=all)는 Azure 웹 사이트에서 확인할 수 있습니다.