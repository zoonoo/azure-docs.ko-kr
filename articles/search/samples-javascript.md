---
title: JavaScript 샘플
titleSuffix: Azure Cognitive Search
description: Azure .NET SDK for JavaScript를 사용 하는 Azure Cognitive Search demo JavaScript 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686856"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 JavaScript 코드 샘플

Azure Cognitive Search의 기능을 설명 하는 JavaScript 코드 샘플에 대해 알아봅니다. 기본 리포지토리는 다음과 같습니다.

| 리포지토리 | 설명 |
|------------|-------------|
| [azure-s e r v e r/s s d/트리/마스터/s i d/검색/검색-문서](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | SDK에서 Azure.Search.Documents client 라이브러리와 함께 제공 되는 Azure SDK 팀이 생성 한 샘플입니다. 클라이언트 라이브러리에 대 한 [단위 테스트](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) 를 검토 하 여 다양 한 api를 호출 하는 방법을 확인할 수도 있습니다. |
| [Azure-샘플/azure-검색-javascript-샘플](https://github.com/Azure-Samples/azure-search-javascript-samples) | [빠른 시작: JavaScript에서 검색 인덱스 만들기](search-get-started-javascript.md)를 비롯 한 방법 문서와 함께 제공 되는 코드 샘플입니다.|

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="javascript-sdk-samples"></a>JavaScript SDK 샘플

Java 용 Azure SDK에는 다양 한 샘플과 패키지 설치, 클라이언트 설정 및 문제 해결을 다루는 [시작 페이지가](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) 포함 되어 있습니다. 이 페이지에서는 사용자 편의를 위해 여기에 나열 된 다음 샘플 범주에 대해서도 설명 합니다.

| 샘플 | Description |
|---------|-------------|
| [인덱스만](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함 되어 있습니다. |
| [dataSourceConnections (인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [인덱서에](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트 하 고, 나열 하 고, 다시 설정 하 고, 삭제 하는 방법을 보여 줍니다.|
| [기술](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | [동의어 맵을](search-synonyms.md)만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.  |
| [쿼리](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 쿼리는 읽기 전용입니다. 이 샘플 쿼리는 Microsoft에서 호스팅하는 공용 인덱스에 대해 실행 됩니다.  |

## <a name="typescript-samples"></a>TypeScript 샘플

SDK는 편의를 위해 여기에 나열 된 TypeScript 예제도 제공 합니다.

| 샘플 | Description |
|---------|-------------|
| [인덱스만](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함 되어 있습니다. |
| [dataSourceConnections (인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [인덱서에](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트 하 고, 나열 하 고, 다시 설정 하 고, 삭제 하는 방법을 보여 줍니다.|
| [기술](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | [동의어 맵을](search-synonyms.md)만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.  |
| [쿼리](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | Microsoft에서 호스팅하는 읽기 전용 공용 인덱스에 대해 쿼리를 실행 하는 방법을 보여 줍니다.  |

## <a name="documentation-samples"></a>설명서 샘플

다음 샘플에는 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/)에 관련 된 문서가 있습니다.

| 샘플 | Description | 
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | 빠른 시작에 대 한 소스 코드 [: JavaScript에서 검색 인덱스를 만듭니다](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>독립 실행형 샘플

| 샘플 | Description |
|---------|-------------|
| [azure-검색-반응-템플릿](https://github.com/dereklegenzoff/azure-search-react-template) | Azure Cognitive Search에 대 한 반응 템플릿 (github.com) |