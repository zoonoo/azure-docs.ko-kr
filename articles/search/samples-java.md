---
title: Java 샘플
titleSuffix: Azure Cognitive Search
description: Java 용 Azure .NET SDK를 사용 하는 Azure Cognitive Search 데모 Java 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955040"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 Java 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 Java 코드 샘플에 대해 알아봅니다. 이러한 샘플은 azure [**SDK For Java**](/azure/developer/java/sdk)용 [**azure Cognitive Search 클라이언트 라이브러리**](/java/api/overview/azure/search-documents-readme) 를 사용 하 여 다음 링크를 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API 참조 | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서는 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 [**azure/azure-sdk-java/tree/master/sdk/search/azure-검색-문서/src/샘플**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) 에서 찾을 수 있습니다.

| 샘플 | Description |
|---------|-------------|
| [인덱스 생성 검색](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | [검색 인덱스](search-what-is-an-index.md)를 만드는 방법을 보여 줍니다. |
| [동의어 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | [동의어 맵을](search-synonyms.md)만드는 방법을 보여 줍니다.  |
| [검색 인덱서 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | [인덱서](search-indexer-overview.md)를 만드는 방법을 보여 줍니다. |
| [검색 인덱서 데이터 원본 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만드는 방법을 보여 줍니다. |
| [기술 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만드는 방법을 보여 줍니다. |
| [문서 로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | [데이터 가져오기](search-what-is-data-import.md) 작업에서 문서를 인덱스에 업로드 하거나 병합 하는 방법을 보여 줍니다. |
| [쿼리 구문](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | [기본 쿼리](search-query-overview.md)를 설정 하는 방법을 보여 줍니다. |

## <a name="doc-samples"></a>Doc 샘플

Cognitive Search 팀의 코드 샘플에서는 기능 및 워크플로를 보여 줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조 됩니다. 이러한 샘플은 GitHub의 [**azure-samples/azure-search-java 샘플**](https://github.com/Azure-Samples/azure-search-java-samples) 에서 찾을 수 있습니다.

| 샘플 | 아티클 | 
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | 빠른 시작에 대 한 소스 코드 [: Java 및 REST에서 검색 인덱스를 만듭니다](search-get-started-java.md). Java SDK에 대 한이 샘플은 업데이트 되지 않았습니다. REST Api를 호출 합니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=java&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플도 Cognitive Search 팀에서 게시 하지만 설명서에서 참조 되지 않습니다. 관련 된 추가 정보 파일은 사용 지침을 제공 합니다.

| 샘플 | Description |
|---------|-------------|
| [검색-java-시작](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | 는 Java SDK 클라이언트 라이브러리를 사용 하 여 검색 인덱스를 만들고 로드 하 고 쿼리 합니다. 이 샘플은 현재 독립 실행형입니다. |
| [검색-java-인덱서-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Java의 Azure Cosmos DB 인덱서를 보여 줍니다. Java SDK에 대 한이 샘플은 업데이트 되지 않았습니다. REST Api를 호출 합니다.|