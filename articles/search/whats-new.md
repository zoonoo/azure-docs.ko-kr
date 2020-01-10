---
title: 새 기능 공지
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 Azure Search 서비스 이름을 포함 하 여 새로운 기능 및 향상 된 기능에 대 한 공지입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834381"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지 하려면이 페이지에 책갈피를 설정 합니다.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure Search에 대 한 새 서비스 이름

Azure Search은 이제 핵심 작업에서 인식 기술 및 AI 처리의 확장 된 사용을 반영 하기 위해 **Azure Cognitive Search** 로 이름이 변경 되었습니다. 인식 기술이 새 기능을 추가 하는 동안 AI를 사용 하는 것은 엄격히 선택 됩니다. AI 없이 Azure Cognitive Search를 계속 사용 하 여 클라우드에서 만들고 관리 하는 인덱스의 개인, 다른 유형의, 텍스트 기반 콘텐츠에 대해 다양 한 전체 텍스트 검색 솔루션을 빌드할 수 있습니다. 

API 버전, Nuget 패키지, 네임 스페이스 및 끝점은 변경 되지 않습니다. 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="feature-announcements"></a>기능 알림

### <a name="january-2020"></a>2020년 1월

+ [고객이 관리 하는 암호화 키](search-security-manage-encryption-keys.md) 가 이제 일반 공급 됩니다. REST를 사용 하는 경우 `api-version=2019-05-06`를 사용 하 여 기능에 액세스할 수 있습니다. 관리 코드의 경우에는 기능이 미리 보기 상태를 벗어나는 경우에도 올바른 패키지는 [.NET SDK 버전 8.0-미리 보기](search-dotnet-sdk-migration-version-9.md) 입니다. 

+ 검색 서비스 끝점에 대 한 *제한 된 IP 액세스 및 개인 끝점 (미리 보기)* 은 이제 **api-version = 2019-10-01.txt-preview**에서 사용할 수 있습니다. [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) 관리 REST API에서 new **IpRule** 및 **networkruleset** 속성을 사용 하 여 보안 끝점을 설정할 수 있습니다. API 버전 및 지역 가용성에 대 한 자세한 내용은 [How to use The Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)항목을 참조 하세요.

### <a name="december-2019"></a>2019년 12월

+ [앱 만들기 (미리 보기)](search-create-app-portal.md) 는 다운로드 가능한 HTML 파일을 생성 하는 포털의 새 마법사입니다. 이 파일에는 검색 서비스의 인덱스에 바인딩된 운영 "localhost" 스타일 웹 앱을 렌더링 하는 포함 된 스크립트가 포함 되어 있습니다. 페이지는 마법사에서 구성할 수 있으며 검색 표시줄, 결과 영역, 사이드바 탐색 및 typeahead.js 쿼리 지원을 포함할 수 있습니다. HTML 오프 라인을 수정 하 여 워크플로 또는 모양을 확장 하거나 사용자 지정할 수 있습니다.

### <a name="november-2019---ignite-conference"></a>11 월 2019-Ignite 회의

+ [증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 는 이미 처리 된 콘텐츠를 손실 하지 않고 특정 단계 또는 단계에 대 한 작업을 수행할 수 있도록 보강 파이프라인에 캐싱 및 statefullness을 추가 합니다. 이전에는 보강 파이프라인에 대 한 모든 변경에 전체 다시 작성이 필요 했습니다. 증분 보강을 사용 하면 비용이 많이 드는 분석 (특히 이미지 분석)의 출력이 보존 됩니다.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [문서 추출 (미리 보기)](cognitive-search-skill-document-extraction.md) 은 기술 내에서 파일의 콘텐츠를 추출할 수 있는 인덱싱 중에 사용 되는 인식 기술입니다. 이전에는 문서 크랙 기술 실행 되기 전에 발생 했습니다. 이 기술 외에도 기술 실행 내에서이 작업을 수행할 수 있습니다.

+ [텍스트 번역 (미리 보기)](cognitive-search-skill-text-translation.md) 은 텍스트를 평가 하는 인덱싱 중에 사용 되는 인식 기술 이며, 각 레코드에 대해 지정 된 대상 언어로 변환 된 텍스트를 반환 합니다.

+ [Power BI 템플릿은](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) Power BI desktop의 기술 자료 저장소에서 보강 콘텐츠를 시각화 하 고 분석할 수 있습니다. 이 템플릿은 [데이터 가져오기 마법사](knowledge-store-create-portal.md)를 통해 만든 Azure table 프로젝션 용으로 설계 되었습니다.

+ [Azure Data Lake Storage Gen2 (미리 보기)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (미리](search-howto-index-cosmosdb.md)보기) 및 [Cosmos DB Cassandra API (미리 보기)](search-howto-index-cosmosdb.md) 는 이제 인덱서에 지원 됩니다. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)사용 하 여 등록할 수 있습니다. 미리 보기 프로그램에 동의 하면 확인 전자 메일을 받게 됩니다.

### <a name="july-2019"></a>2019년 7월

+ 일반적으로 [Azure Government 클라우드에서](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)사용할 수 있습니다.

## <a name="service-updates"></a>서비스 업데이트

Azure Cognitive Search에 대 한 [서비스 업데이트 공지](https://azure.microsoft.com/updates/?product=search&status=all) 는 azure 웹 사이트에서 찾을 수 있습니다.