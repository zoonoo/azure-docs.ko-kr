---
title: Java 샘플
titleSuffix: Azure Cognitive Search
description: Java 용 Azure .NET SDK를 사용 하는 Azure Cognitive Search 데모 Java 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 26e30b42906a3d8d7a3fcdc013537104a85f32fe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701860"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 Java 코드 샘플

Azure Cognitive Search의 기능을 설명 하는 Java 코드 샘플에 대해 알아봅니다. 기본 리포지토리는 다음과 같습니다.

| 리포지토리 | Description |
|------------|-------------|
| [azure-sdk-java/tree/master/sdk/search/azure-검색-documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | SDK에서 Azure.Search.Documents client 라이브러리와 함께 제공 되는 Azure SDK 팀이 생성 한 샘플입니다. 클라이언트 라이브러리에 대 한 [단위 테스트](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) 를 검토 하 여 다양 한 api를 호출 하는 방법을 확인할 수도 있습니다. |
| [Azure-샘플/azure-검색-java-샘플](https://github.com/Azure-Samples/azure-search-java-samples) | 방법 문서와 함께 제공 되는 코드 샘플입니다. **이 리포지토리의 샘플은 Java 용 AZURE SDK 사용으로 아직 업데이트 되지 않았습니다**. 현재 이러한 샘플은 Java 코드에서 REST Api를 호출 합니다.|

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="java-sdk-samples"></a>Java SDK 샘플

Java 용 Azure SDK에는 다양 한 샘플과 패키지 설치를 다루는 [시작 페이지가](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) 포함 되어 있습니다. 이 페이지에는 다양 한 예제도 나열 되어 있습니다. 사용자 편의를 위해 다음과 같은 몇 가지 일반적인 작업을 아래에 나열 합니다.

| 샘플 | Description |
|---------|-------------|
| [인덱스 생성 검색](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | [검색 인덱스](search-what-is-an-index.md)를 만드는 방법을 보여 줍니다. |
| [동의어 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | [동의어 맵을](search-synonyms.md)만드는 방법을 보여 줍니다.  |
| [검색 인덱서 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | [인덱서](search-indexer-overview.md)를 만드는 방법을 보여 줍니다. |
| [검색 인덱서 데이터 원본 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만드는 방법을 보여 줍니다. |
| [기술 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  연결 된 인덱서이 고 인덱싱 중 AI 기반 보강를 수행 하는 [기술력과](cognitive-search-working-with-skillsets.md) 를 만드는 방법을 보여 줍니다. |
| [문서 로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | [데이터 가져오기](search-what-is-data-import.md) 작업에서 문서를 인덱스에 업로드 하거나 병합 하는 방법을 보여 줍니다. |
| [쿼리 구문](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | [기본 쿼리](search-query-overview.md)를 설정 하는 방법을 보여 줍니다. |

## <a name="documentation-samples"></a>설명서 샘플

다음 샘플에는 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/)에 관련 된 문서가 있습니다.

| 샘플 | Description | 
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | 빠른 시작에 대 한 소스 코드 [: Java에서 검색 인덱스를 만듭니다](search-get-started-java.md). 이 샘플에서는 REST Api를 호출 합니다. |
| [검색-java-인덱서-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Java의 Azure Cosmos DB 인덱서를 보여 줍니다. 이 샘플에서는 REST Api를 호출 합니다. |