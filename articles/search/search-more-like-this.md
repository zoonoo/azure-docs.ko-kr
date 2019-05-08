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
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024666"
---
# <a name="morelikethis-in-azure-search-preview"></a>Azure Search의 moreLikeThis(미리 보기)

`moreLikeThis=[key]` 쿼리 매개 변수를 [검색 문서 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 문서 발견 하는 문서 키로 지정 된 문서와 비슷합니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 모든 검색 가능 필드의 내용을 고려 하는 경우 제한 된 필드를 사용 하 여 지정한 빼기는 `searchFields` 매개 변수입니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 검색 가능 필드의 내용으로 간주 됩니다. 대신 특정 필드를 지정 하려는 경우 사용할 수 있습니다는 `searchFields` 매개 변수입니다. 

> [!NOTE]
> `moreLikeThis` 미리 보기에서 검색할 수 있는 하위 필드에서 작동 하지 않습니다는 [복합 형식](search-howto-complex-data-types.md)합니다.

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

## <a name="feature-availability"></a>기능 가용성

합니다 `moreLikeThis` 매개 변수는 미리 보기 REST Api 에서만 사용할 수 있습니다 (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>다음 단계

이 기능을 사용 하 여 실험에 웹 테스트 도구를 사용할 수 있습니다.  이 연습에 대 한 Postman을 사용 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Postman을 사용 하 여 Azure Search REST Api 탐색](search-fiddler.md)