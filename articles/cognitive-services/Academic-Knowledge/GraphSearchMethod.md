---
title: Graph Search 메서드 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Graph Search 메서드를 사용하여 특정 그래프 패턴을 기반으로 일련의 교육 기관을 반환합니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339135"
---
# <a name="graph-search-method"></a>Graph Search 메서드

**Graph Search** REST API는 지정된 그래프 패턴을 기반으로 일련의 교육 기관을 반환하는 데 사용됩니다.  응답은 사용자 지정 제약 조건을 만족하는 그래프 경로 집합입니다. 그래프 경로는 _v0, e0, v1, e1, ..., vn_의 형태로 그래프 노드와 에지의 인터리브된 시퀀스이며, 여기서 _v0_은 경로의 시작 노드입니다.
<br>

**REST 엔드포인트:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>요청 매개 변수  

이름     | 값 | Required?  | 설명
-----------|-----------|---------|--------
**mode**       | 텍스트 문자열 | 예 | 사용할 모드의 이름입니다. 값은 *json* 또는 *lambda*입니다.

Graph Search 메서드는 HTTP POST 요청을 통해 호출되어야 합니다. post 요청은 **application/json** 콘텐츠 형식 헤더를 포함해야 합니다.

##### <a name="json-search"></a>JSON 검색 

*json* 검색의 경우, POST 본문은 JSON 개체입니다. JSON 개체는 사용자 지정 제약 조건이 있는 경로 패턴을 설명합니다(*json* 검색을 위한 [JSON 개체의 사양](JSONSearchSyntax.md) 참조).


##### <a name="lambda-search"></a>람다 검색

*람다* 검색의 경우, POST 본문은 일반 텍스트 문자열입니다. POST 본문은 단일 C# 문인 LIKQ 람다 쿼리 문자열입니다(*람다* 검색을 위한 [쿼리 문자열의 사양](LambdaSearchSyntax.md) 참조). 

<br>

## <a name="response-json"></a>응답(JSON)

이름 | 설명
-------|-----   
**결과** | 쿼리 식과 일치하는 0개 이상의 엔터티 배열입니다. 각 엔터티에는 요청된 특성 값이 있습니다. 이 필드는 요청이 성공적으로 처리된 경우 나타납니다.
**error** | HTTP 상태 코드. 이 필드는 요청이 실패하는 경우 나타납니다.
**message** | 오류 메시지입니다. 이 필드는 요청이 실패하는 경우 나타납니다.

쿼리를 _800ms_ 내에 처리할 수 없는 경우 _시간 제한_ 오류가 반환됩니다. 

<br>

#### <a name="example"></a>예제:

##### <a name="json-search"></a>JSON 검색
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
*json* 검색의 경우, 제목에 “그래프 엔진”이 포함되고 작성자가 “bin shao”인 문서를 가져오려면 다음과 같이 쿼리를 지정할 수 있습니다.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

쿼리의 출력은 그래프 경로의 배열입니다. 그래프 경로는 쿼리 경로에 지정된 노드에 해당하는 노드 개체의 배열입니다. 이러한 노드 개체에는 엔터티 ID를 나타내는 적어도 하나의 속성 *CellID*가 있습니다. 다른 속성은 [*통과 동작 개체*](JSONSearchSyntax.md)의 선택 연산자를 통해 속성 이름을 지정하여 검색할 수 있습니다.

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>람다 검색 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
*람다* 검색의 경우, 지정된 문서의 작성자 ID를 얻으려면 다음과 같은 쿼리를 작성할 수 있습니다.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

*람다* 검색 쿼리의 출력은 또한 그래프 경로의 배열입니다.

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>그래프 스키마

그래프 스키마는 그래프 검색 쿼리를 작성하는 데 유용합니다. 다음 그림에 나와 있습니다.

![Microsoft Academic Graph 스키마](./Images/AcademicGraphSchema.png)
