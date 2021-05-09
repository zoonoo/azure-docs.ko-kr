---
title: '.NET 자습서: 검색 통합 하이라이트'
titleSuffix: Azure Cognitive Search
description: 이 치트 시트를 사용하면 검색 사용 웹 사이트에서 사용되는 .NET SDK 검색 통합 쿼리를 이해할 수 있습니다.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 0f4f2b2c87f295aa98257521c420902a4e7302f7
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163592"
---
# <a name="4---net-search-integration-cheat-sheet"></a>4 - .NET 검색 통합 치트 시트

이전 단원에서는 정적 웹앱에 검색을 추가했습니다. 이 단원에서는 통합을 설정하는 필수 단계를 중점적으로 설명합니다. 검색을 웹앱에 통합하는 방법에 대한 치트 시트를 찾고 있는 경우 이 문서에서는 알아야 할 사항을 설명합니다.

다음 애플리케이션을 사용할 수 있습니다. 
* [샘플](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [데모 웹 사이트 - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearchdocuments"></a>Azure SDK Azure.Search.Documents

함수 앱은 Cognitive Search용 Azure SDK를 사용합니다.

* NuGet: [Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
* 참조 설명서: [클라이언트 라이브러리](/dotnet/api/overview/azure/search)

함수 앱은 리소스 이름, 리소스 키 및 인덱스 이름을 사용하여 SDK를 통해 클라우드 기반 Cognitive Search API를 인증합니다. 비밀은 정적 웹앱 설정에 저장되고 함수에 환경 변수로 끌어옵니다. 

## <a name="configure-secrets-in-a-localsettingsjson-file"></a>local.settings.json 파일에서 비밀 구성

1. `./api/`에서 `local.settings.json`라는 새 파일을 만들고 다음 JSON 개체를 파일에 복사합니다.

    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "",
        "FUNCTIONS_WORKER_RUNTIME": "dotnet",
        "SearchApiKey": "YOUR_SEARCH_QUERY_KEY",
        "SearchServiceName": "YOUR_SEARCH_RESOURCE_NAME",
        "SearchIndexName": "good-books"
      }
    }
    ```

1. 다음을 소유자 검색 리소스 값으로 변경합니다. 
    * YOUR_SEARCH_RESOURCE_NAME
    * YOUR_SEARCH_QUERY_KEY

## <a name="azure-function-search-the-catalog"></a>Azure 함수: 카탈로그 검색

`Search` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Search.cs)는 검색어를 사용하여 검색 인덱스의 문서를 검색하고 일치하는 항목 목록을 반환합니다. 

Azure 함수는 검색 구성 정보를 가져와서 쿼리를 충족합니다.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Search.cs" highlight="22-24, 55" :::

## <a name="client-search-from-the-catalog"></a>클라이언트: 카탈로그에서 검색

다음 코드를 사용하여 React 클라이언트에서 Azure 함수를 호출합니다. 

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure 함수: 카탈로그의 제안

`Suggest` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Suggest.cs)는 사용자가 입력하는 동안 검색어를 사용하고, 검색 인덱스의 문서 전체에서 책 제목 및 저자와 같은 검색어를 제안하여 작은 일치 항목 목록을 반환합니다. 

검색 제안기 `sg`는 대량 업로드 중에 사용되는 [스키마 파일](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/bulk-insert/BookSearchIndex.cs)에 정의되어 있습니다.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Suggest.cs" highlight="21-23, 50-52" :::

## <a name="client-suggestions-from-the-catalog"></a>클라이언트: 카탈로그의 제안

제안 함수 API는 구성 요소 초기화의 일환으로 `\src\components\SearchBar\SearchBar.js`의 React 앱에서 호출됩니다.

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure 함수: 특정 문서 가져오기 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Lookup.cs)는 ID를 사용하여 검색 인덱스에서 문서 개체를 반환합니다. 

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Lookup.cs" highlight="19-21, 42" :::

## <a name="client-get-specific-document"></a>클라이언트: 특정 문서 가져오기 

이 함수 API는 구성 요소 초기화의 일환으로 `\src\pages\Details\Detail.js`의 React 앱에서 호출됩니다.

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="c-models-to-support-function-app"></a>함수 앱을 지원하는 C# 모델

이 앱에서 함수를 지원하는 데 사용되는 모델은 다음과 같습니다.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Models.cs" :::

## <a name="next-steps"></a>다음 단계

* [Azure SQL 데이터 인덱싱](search-indexer-tutorial.md)
