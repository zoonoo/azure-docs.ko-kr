---
title: 더좋아이 (미리 보기) 쿼리 기능
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 REST API의 미리 보기 버전에서 사용할 수 있는 더Like이(미리 보기) 기능에 대해 설명합니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873814"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 인지 검색에서 이것(미리 보기)과 유사합니다.

> [!IMPORTANT] 
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK 지원이 없습니다.

`moreLikeThis=[key]`는 문서 키에서 지정한 문서와 유사한 문서를 찾는 [검색 문서 API의](https://docs.microsoft.com/rest/api/searchservice/search-documents) 쿼리 매개 변수입니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 `searchFields` 매개 변수를 사용하여 지정한 제한된 필드를 제외한 모든 검색 가능한 필드의 내용이 고려됩니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 검색 필드의 내용이 고려됩니다. 대신 특정 필드를 지정하려는 경우 매개 `searchFields` 변수를 사용할 수 있습니다. 

복잡한 `MoreLikeThis` [형식의](search-howto-complex-data-types.md)검색 가능한 하위 필드에서는 사용할 수 없습니다.

## <a name="examples"></a>예

다음 의 모든 예제에서는 [빠른 시작: Azure 포털에서 검색 인덱스 만들기의](search-get-started-portal.md)호텔 샘플을 사용합니다.

### <a name="simple-query"></a>단순 쿼리

다음 쿼리는 `moreLikeThis` 설명 필드가 매개 변수에 의해 지정된 원본 문서의 필드와 가장 유사한 문서를 찾습니다.

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

이 예에서 요청은 29가 있는 호텔과 `HotelId` 유사한 호텔을 검색합니다.
HTTP GET을 사용하는 대신 HTTP POST를 `MoreLikeThis` 사용하여 호출할 수도 있습니다.

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>필터 적용

`MoreLikeThis`와 같은 `$filter`다른 일반적인 쿼리 매개 변수와 결합할 수 있습니다. 예를 들어, 검색어는 카테고리가 '예산'이고 등급이 3.5보다 높은 호텔로만 제한될 수 있습니다.

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>필드 선택 및 결과 제한

`$top` 선택기를 사용하여 `MoreLikeThis` 쿼리에서 반환해야 하는 결과 수를 제한할 수 있습니다. 또한 `$select`필드를 에서 선택할 수 있습니다. 여기에 상위 3 개 호텔은 ID, 이름 및 등급과 함께 선택됩니다 : 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>다음 단계

모든 웹 테스트 도구를 사용하여 이 기능을 실험할 수 있습니다.  이 연습에는 우체부(Postman)를 사용하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [우체부를 사용하여 Azure 인지 검색 REST API 살펴보기](search-get-started-postman.md)
