---
title: moreLikeThis (미리 보기) 쿼리 기능
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API의 미리 보기 버전에서 제공 되는 moreLikeThis (미리 보기) 기능에 대해 설명 합니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793465"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Cognitive Search의 moreLikeThis (미리 보기)

> [!Note]
> moreLikeThis는 미리 보기로 제공 되며 프로덕션 용도로는 사용할 수 없습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.

`moreLikeThis=[key]`은 문서 키로 지정 된 문서와 유사한 문서를 찾는 [문서 검색 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 의 쿼리 매개 변수입니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 `searchFields` 매개 변수를 사용 하 여 지정한 제한 된 필드를 제외한 모든 검색 가능 필드의 내용이 고려 됩니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 수준 검색 가능 필드의 내용을 고려 합니다. 대신 특정 필드를 지정 하려면 `searchFields` 매개 변수를 사용 하면 됩니다. 

[복합 형식의](search-howto-complex-data-types.md)검색 가능한 하위 필드에는 moreLikeThis을 사용할 수 없습니다.

## <a name="examples"></a>예시 

다음은 moreLikeThis 쿼리의 예입니다. 이 쿼리는 해당 설명 필드가 `moreLikeThis` 매개 변수로 지정된 대로 소스 문서의 필드에 가장 유사한 문서를 찾습니다.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>다음 단계

모든 웹 테스트 도구를 사용 하 여이 기능을 시험해 볼 수 있습니다.  이 연습에서는 Postman을 사용 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Postman을 사용 하 여 Azure Cognitive Search REST Api 살펴보기](search-get-started-postman.md)