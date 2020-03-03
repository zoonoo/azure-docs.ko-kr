---
title: 새 기능 공지
titleSuffix: Azure Cognitive Search
description: 서비스 이름을 Azure Search에서 Azure Cognitive Search로 변경하는 것을 포함하여 새로운 기능과 향상된 기능에 대한 공지 사항입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/30/2020
ms.openlocfilehash: 74f69f789ecb0d529e52054eeb1ac7ccb51bd6c2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588828"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지하려면 이 페이지에 대한 책갈피를 지정하세요.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>새 서비스 이름

핵심 작업에서 확장된 인지 기술과 AI 처리(아직 선택 사항)를 사용할 수 있도록 Azure Search의 이름이 이제 **Azure Cognitive Search**로 변경되었습니다. API 버전, NuGet 패키지, 네임스페이스 및 엔드포인트는 변경되지 않았습니다. 새 검색 솔루션과 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="feature-announcements"></a>기능 공지

### <a name="february-2020"></a>2020년 2월

+ [PII 검색(미리 보기)](cognitive-search-skill-pii-detection.md)는 인덱싱 중에 사용되는 인지 기술로서, 입력 텍스트에서 개인 식별이 가능한 정보를 추출하여 다양한 방법으로 해당 텍스트에서 마스킹할 수 있는 옵션을 제공합니다.

+ [사용자 지정 엔터티 조회(미리 보기)](cognitive-search-skill-custom-entity-lookup.md )는 사용자 지정된 단어 및 구의 사용자 정의 목록에서 텍스트를 찾습니다. 이 목록을 사용하면 일치하는 엔터티가 있는 모든 문서에 레이블이 지정됩니다. 또한 이 기술은 비슷하지만 정확하지 않는 일치 항목을 찾는 데 적용할 수 있는 유사 항목 일치 수준을 지원합니다. 

### <a name="january-2020"></a>2020년 1월

+ [고객이 관리하는 암호화 키](search-security-manage-encryption-keys.md)는 이제 일반 공급됩니다. REST를 사용하는 경우 `api-version=2019-05-06`을 사용하여 기능에 액세스할 수 있습니다. 관리 코드의 경우 기능이 미리 보기 범위를 벗어나더라도 올바른 패키지는 여전히 [.NET SDK 버전 8.0-preview](search-dotnet-sdk-migration-version-9.md)입니다. 

+ 검색 서비스에 대한 프라이빗 액세스는 현재 미리 보기에 있는 두 가지 메커니즘을 통해 제공됩니다.

  + `api-version=2019-10-01-Preview` 관리 REST API를 사용하여 서비스를 만들면 특정 IP 주소에 대한 액세스를 제한할 수 있습니다. 미리 보기 API의 [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate)에는 새로운 **IpRule** 및 **NetworkRuleSet** 속성이 있습니다. 이 미리 보기 기능은 선택한 지역에서 사용할 수 있습니다. 자세한 내용은 [관리 REST API를 사용하는 방법](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)을 참조하세요.

  + 이 액세스는 현재 제한된 액세스 미리 보기를 통해 사용할 수 있으며, 동일한 가상 네트워크의 클라이언트에서 연결하기 위해 Azure 프라이빗 엔드포인트를 지원하는 Azure Search 서비스를 프로비저닝할 수 있습니다. 자세한 내용은 [보안 연결을 위한 프라이빗 엔드포인트 만들기](service-create-private-endpoint.md)를 참조하세요.

### <a name="december-2019"></a>2019년 12월

+ [앱 만들기(미리 보기)](search-create-app-portal.md)는 다운로드 가능한 HTML 파일을 생성하는 포털의 새 마법사입니다. 이 파일에는 검색 서비스의 인덱스에 바인딩된 작동 가능한 "localhost" 스타일의 웹앱을 렌더링하는 포함 스크립트가 포함되어 있습니다. 페이지는 마법사에서 구성할 수 있으며 검색 창, 결과 영역, 사이드바 탐색 및 자동 완성 쿼리 지원을 포함할 수 있습니다. 워크플로 또는 모양을 확장하거나 사용자 지정하기 위해 HTML을 오프라인에서 수정할 수 있습니다.

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

## <a name="service-updates"></a>서비스 업데이트

Azure Cognitive Search에 대한 [서비스 업데이트 공지](https://azure.microsoft.com/updates/?product=search&status=all)는 Azure 웹 사이트에서 확인할 수 있습니다.