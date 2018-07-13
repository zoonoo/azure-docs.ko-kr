---
title: Knowledge Exploration Service API의 Evaluate 메서드 | Microsoft Docs
description: Cognitive Services의 KES(Knowledge Exploration Service) API에서 Evaluate 메서드를 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373111"
---
# <a name="evaluate-method"></a>evaluate 메서드
*evaluate* 메서드는 인덱스 데이터를 기반으로 구조화된 쿼리 식의 출력을 평가 및 반환합니다.

일반적으로 언어 식은 interpret 메서드에 대한 응답에서 얻습니다.  하지만 쿼리 식을 직접 구성할 수도 있습니다([구조화된 쿼리 식](Expressions.md) 참조).  

## <a name="request"></a>요청 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name|값|설명
----|----|----
expr       | 텍스트 문자열 | 인덱스 엔터티 하위 집합을 선택하는 구조화된 쿼리 식입니다.
특성 | 텍스트 문자열 | 응답에 포함되는 쉼표로 구분된 특성 목록입니다.
count      | 숫자(기본값=10) | 반환할 최대 결과 수입니다.
offset     | 숫자(기본값=0) | 반환할 첫 번째 결과의 인덱스입니다.
orderby |   텍스트 문자열 | 결과를 정렬하는 데 사용되는 특성 이름이며, 그 뒤에는 선택적 정렬 순서(기본값=asc): "*attrname*[:(asc&#124;desc)]"이 옵니다.  지정하지 않으면 자연 로그 확률 내림차순으로 결과가 반환됩니다.
시간 제한  | 숫자(기본값=1000) | 시간 제한(밀리초)입니다. 시간 제한이 경과되기 전에 계산된 결과만 반환됩니다.

*count* 및 *offset* 매개 변수를 사용하여 여러 요청에 대한 더 많은 수의 결과를 증분 방식으로 얻을 수 있습니다.
  
## <a name="response-json"></a>응답(JSON)
JSONPath|설명
----|----
$.expr | 요청의 *expr* 매개 변수입니다.
$.entities | 구조화된 쿼리 식과 일치하는 0개 이상의 개체 엔터티 배열입니다. 
$.aborted | 요청 시간이 초과되면 True입니다.

각 엔터티에는 *logprob* 값과 요청된 특성의 값이 포함됩니다.

## <a name="example"></a>예
학술 저서 예제에서 다음 요청은 구조화된 쿼리 식(*interpret* 요청의 출력에서 얻은)을 전달하고, 일치하는 상위 엔터티 2개에 대한 몇 가지 특성을 검색합니다.

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

응답에는 일치할 가능성이 가장 높은 상위 엔터티 2개("count=2")가 포함됩니다.  각 엔터티에 대한 제목, 연도, 작성자 이름 및 작성자 ID 특성이 반환됩니다.  복합 구조 특성 값이 데이터 파일에서 지정되는 방식과 어떻게 일치하는지 살펴보세요. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
