---
title: 맞춤법 검사 추가
titleSuffix: Azure Cognitive Search
description: 쿼리를 실행 하기 전에 쿼리 사용 조건에 대 한 오류를 수정 하려면 쿼리 파이프라인에 철자 수정 기능을 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cdc5de8153e8b2e0ea8bb8ea372fe8610ccb895b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679832"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search에서 쿼리에 맞춤법 검사 추가

> [!IMPORTANT]
> 맞춤법 수정은 공개 미리 보기로 제공 되며 미리 보기 REST API 통해서만 제공 됩니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다. 초기 미리 보기를 시작 하는 동안 맞춤법 검사기에 대 한 요금이 청구 되지 않습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

검색 엔진에 도달 하기 전에 개별 검색 쿼리 용어를 철자 수정 하 여 회수를 향상 시킬 수 있습니다. **맞춤법 검사기** 매개 변수는 모든 쿼리 유형 ( [단순](query-simple-syntax.md), [전체](query-lucene-syntax.md)및 현재 공개 미리 보기로 제공 되는 새 [의미](semantic-how-to-query-request.md) 옵션)에 대해 지원 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 영어 콘텐츠를 포함 하는 기존 검색 인덱스

+ 쿼리를 보내기 위한 검색 클라이언트

  검색 클라이언트는 쿼리 요청에 대 한 미리 보기 REST Api를 지원 해야 합니다. 미리 보기 Api에 대 한 REST 호출을 수행 하기 위해 수정한 [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 코드를 사용할 수 있습니다.

+ 맞춤법 수정 기능을 사용 하는 [쿼리 요청](/rest/api/searchservice/preview-api/search-documents) 에는 "api-version = 2020-06 -30-Preview", "맞춤법 검사기 = 어휘" 및 "queryLanguage = en-us"가 있습니다.

  맞춤법 검사기에는 queryLanguage가 필요 하 고 현재 "en-us"는 유일 하 게 올바른 값입니다.

> [!Note]
> 맞춤법 검사기 매개 변수는 의미 체계 검색을 제공 하는 동일한 지역에서 모든 계층에서 사용할 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

## <a name="spell-correction-with-simple-search"></a>단순 검색으로 맞춤법 수정

다음 예에서는 기본 제공 호텔 샘플 인덱스를 사용 하 여 간단한 자유 형식 텍스트 쿼리에 대 한 맞춤법 수정 사항을 보여 줍니다. 맞춤법 수정이 없으면 쿼리가 0 개의 결과를 반환 합니다. 수정 기능을 사용 하는 경우 쿼리는 Johnson의 패밀리 지향 리조트에 대해 하나의 결과를 반환 합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>전체 Lucene으로 맞춤법 수정

분석을 수행 하는 개별 쿼리 용어에 대 한 맞춤법 수정이 발생 하므로 일부 Lucene 쿼리에서는 맞춤법 검사기 매개 변수를 사용할 수 있지만 다른 항목은 사용할 수 없습니다.

+ 텍스트 분석을 우회 하는 호환 되지 않는 쿼리 형식은 와일드 카드, regex, 유사 항목을 포함 합니다.
+ 호환 되는 쿼리 양식: 필드 지정 검색, 근접, 용어 부스트

이 예제에서는 전체 Lucene 구문 및 철자가 틀린 쿼리 용어를 사용 하 여 범주 필드에 대해 필드 지정 search를 사용 합니다. 맞춤법 검사기를 포함 하 여 "Suiite"의 철자가 수정 되 고 쿼리가 성공 합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>의미 체계 검색을 사용 하 여 맞춤법 수정

이 쿼리는 하나를 제외한 모든 용어에서 오타를 사용 하 여 관련 결과를 반환 하도록 맞춤법을 수정 합니다. 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조 하세요.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>언어 관련 고려 사항

맞춤법 검사기에 필요한 queryLanguage 매개 변수는 인덱스 스키마의 필드 정의에 할당 된 [언어 분석기](index-add-language-analyzers.md) 와 일치 해야 합니다. 쿼리 요청에 지정 된 queryLanguage는 맞춤법 검사에 사용 되는 lexicons를 결정 하 고, 사용 하는 경우 [의미 체계 순위 알고리즘](semantic-how-to-query-response.md) 에 대 한 입력으로도 사용 됩니다. 언어 분석기는 인덱싱 중에 그리고 검색 인덱스에서 일치 하는 문서를 검색 하는 동안 사용 됩니다. 일관성을 유지 하기 위해 queryLanguage가 "en-us" 인 경우 모든 언어 분석기는 영어 변형 ("en-us" 또는 "en. lucene") 이어야 합니다. 

> [!NOTE]
> 언어와 관계 없는 분석기 (예: 키워드, 단순, 표준, 중지, 공백 또는 `standardasciifolding.lucene` )는 queryLanguage 설정과 충돌 하지 않습니다.

쿼리 요청에서 설정 하는 queryLanguage는 맞춤법 검사기, 답변 및 캡션에 동일 하 게 적용 됩니다. 개별 파트에 대 한 재정의가 없습니다.

검색 인덱스의 콘텐츠는 여러 언어로 구성 될 수 있지만 쿼리 입력은 대부분의 언어로 구성 될 가능성이 높습니다. 검색 엔진은 queryLanguage, 언어 분석기 및 콘텐츠가 구성 된 언어의 호환성을 확인 하지 않으므로 잘못 된 결과가 발생 하지 않도록 쿼리 범위를 적절 하 게 결정 해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)
+ [기본 쿼리 만들기](search-query-create.md)
+ [Full Lucene 쿼리 구문 사용](query-Lucene-syntax.md)
+ [단순 쿼리 구문 사용](query-simple-syntax.md)
+ [의미 체계 검색 개요](semantic-search-overview.md)