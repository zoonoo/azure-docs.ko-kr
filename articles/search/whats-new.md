---
title: 새 기능 공지
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 Azure Search 서비스 이름을 포함 하 여 새로운 기능 및 향상 된 기능에 대 한 공지입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1df328f151a4085ec0aadd1b880048f81483a51
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901319"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지 하려면이 페이지에 책갈피를 설정 합니다.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure Search에 대 한 새 서비스 이름

Azure Search은 이제 핵심 작업에서 인식 기술 및 AI 처리의 확장 된 사용을 반영 하기 위해 **Azure Cognitive Search** 로 이름이 변경 되었습니다. 인식 기술이 새 기능을 추가 하는 동안 AI를 사용 하는 것은 엄격히 선택 됩니다. AI 없이 Azure Cognitive Search를 계속 사용 하 여 클라우드에서 만들고 관리 하는 인덱스의 개인, 다른 유형의, 텍스트 기반 콘텐츠에 대해 다양 한 전체 텍스트 검색 솔루션을 빌드할 수 있습니다. 

API 버전, Nuget 패키지, 네임 스페이스 및 끝점은 변경 되지 않습니다. 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="feature-announcements"></a>기능 알림

### <a name="december-2019"></a>12 월 2019

+ [앱 만들기 (미리 보기)](search-create-app-portal.md) 는 다운로드 가능한 HTML 파일을 생성 하는 포털의 새 마법사입니다. 이 파일에는 검색 서비스의 인덱스에 바인딩된 운영 "localhost" 스타일 웹 앱을 렌더링 하는 포함 된 스크립트가 포함 되어 있습니다. 페이지는 마법사에서 구성할 수 있으며 검색 표시줄, 결과 영역, 사이드바 탐색 및 typeahead.js 쿼리 지원을 포함할 수 있습니다. HTML 오프 라인을 수정 하 여 워크플로 또는 모양을 확장 하거나 사용자 지정할 수 있습니다.

### <a name="november-2019---ignite-conference"></a>11 월 2019-Ignite 회의

+ [증분 인덱싱 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 을 사용 하면 보강 파이프라인을 수정할 때 다시 처리할 단계를 선택 하 고 선택할 수 있습니다. 증분 인덱싱은 이전에 분석 한 이미지 내용이 있는 경우에 유용 합니다. 비용이 많이 드는 분석의 출력은 저장 된 후 추가 인덱싱 또는 보강의 기준으로 사용 됩니다.

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