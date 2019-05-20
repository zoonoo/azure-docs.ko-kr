---
title: Azure Search의 moreLikeThis(미리 보기) - Azure Search
description: Azure Search REST API에서 노출된 moreLikeThis(미리 보기) 기능에 대한 예비 설명서입니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522917"
---
# <a name="morelikethis-in-azure-search"></a>Azure Search의 moreLikeThis

> [!Note]
> moreLikeThis 미리 보기로 제공 되며 프로덕션 사용에 대 한 용도 아니지만 됩니다. 합니다 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md) 이 기능을 제공 합니다. 이 이번에는.NET SDK 지원은 없습니다.

`moreLikeThis=[key]` 쿼리 매개 변수를 [검색 문서 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 문서 발견 하는 문서 키로 지정 된 문서와 비슷합니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 모든 검색 가능 필드의 내용을 고려 하는 경우 제한 된 필드를 사용 하 여 지정한 빼기는 `searchFields` 매개 변수입니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 검색 가능 필드의 내용으로 간주 됩니다. 대신 특정 필드를 지정 하려는 경우 사용할 수 있습니다는 `searchFields` 매개 변수입니다. 

MoreLikeThis의 하위 검색 가능 필드를 사용할 수 없습니다는 [복합 형식](search-howto-complex-data-types.md)합니다.

## <a name="examples"></a>예 

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

이 기능을 사용 하 여 실험에 웹 테스트 도구를 사용할 수 있습니다.  이 연습에 대 한 Postman을 사용 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Postman을 사용 하 여 Azure Search REST Api 탐색](search-fiddler.md)