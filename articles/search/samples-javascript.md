---
title: JavaScript 샘플
titleSuffix: Azure Cognitive Search
description: Azure .NET SDK for JavaScript를 사용 하는 Azure Cognitive Search demo JavaScript 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955023"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 JavaScript 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 JavaScript 코드 샘플에 대해 알아봅니다. 이러한 샘플은 azure [**SDK For JavaScript**](/azure/developer/javascript/)에 대 한 [**azure Cognitive Search 클라이언트 라이브러리**](/javascript/api/overview/azure/search-documents-readme) 를 사용 하 여 다음 링크를 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API 참조 | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서는 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 azure-s e r v e r-s s t/ [**트리/마스터/s i d/검색/검색-문서/샘플**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) 에서 찾을 수 있습니다.

### <a name="javascript-sdk-samples"></a>JavaScript SDK 샘플

| 샘플 | Description |
|---------|-------------|
| [인덱스만](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함 되어 있습니다. |
| [dataSourceConnections (인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [인덱서에](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 다시 설정 하 고, 삭제 하는 방법을 보여 줍니다.|
| [기술](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | [동의어 맵을](search-synonyms.md)만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.  |
| [쿼리](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Microsoft에서 호스팅하는 읽기 전용 공용 인덱스에 대해 쿼리를 실행 하는 방법을 보여 줍니다.  |

### <a name="typescript-samples"></a>TypeScript 샘플

| 샘플 | Description |
|---------|-------------|
| [인덱스만](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함 되어 있습니다. |
| [dataSourceConnections (인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [인덱서에](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 다시 설정 하 고, 삭제 하는 방법을 보여 줍니다.|
| [기술](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | [동의어 맵을](search-synonyms.md)만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.  |
| [쿼리](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Microsoft에서 호스팅하는 읽기 전용 공용 인덱스에 대해 쿼리를 실행 하는 방법을 보여 줍니다.  |

## <a name="doc-samples"></a>Doc 샘플

Cognitive Search 팀의 코드 샘플에서는 기능 및 워크플로를 보여 줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조 됩니다. 이러한 샘플은 GitHub의 [**azure-samples/azure-search-javascript 샘플**](https://github.com/Azure-Samples/azure-search-javascript-samples) 에서 찾을 수 있습니다.

| 샘플 | 기사 |
|---------|---------|
| [빠른](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | 빠른 시작에 대 한 소스 코드 [: JavaScript에서 검색 인덱스를 만듭니다](search-get-started-javascript.md). 이 문서에서는 샘플 데이터를 사용 하 여 검색 인덱스를 만들고 로드 하 고 쿼리 하는 기본 워크플로를 다룹니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=javascript&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플도 Cognitive Search 팀에서 게시 하지만 설명서에서 참조 되지 않습니다. 관련 된 추가 정보 파일은 사용 지침을 제공 합니다.

| 샘플 | Description |
|---------|-------------|
| [azure-검색-반응-템플릿](https://github.com/dereklegenzoff/azure-search-react-template) | Azure Cognitive Search에 대 한 반응 템플릿 (github.com) |