---
title: Evaluate 메서드 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Evaluate 메서드를 사용하여 쿼리 식에 따라 일련의 교육 기관을 반환합니다.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: c3449d3b61c9b8950c0530590e7f1950c06afbc3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187303"
---
# <a name="evaluate-method"></a>Evaluate 메서드

**Evaluate** REST API는 쿼리 식에 따라 일련의 교육 기관을 반환하는 데 사용됩니다.
<br>

**REST 엔드포인트:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>요청 매개 변수  
Name     | 값 | Required?  | 설명
-----------|-----------|---------|--------
**expr**       | 텍스트 문자열 | 예 | 반환할 엔터티를 지정하는 쿼리 식입니다.
**model**      | 텍스트 문자열 | 아니요  | 쿼리할 모델의 이름입니다.  현재 기본값은 *latest*입니다.        
**attributes** | 텍스트 문자열 | 아니요<br>기본값: Id | 응답에 포함된 특성 값을 지정하는 쉼표로 구분된 목록입니다. 특성 이름은 대/소문자를 구분합니다.
**count**        | Number | 아니요<br>Default: 10 | 반환할 결과의 수입니다.
**offset**     | Number |   아니요<br>Default: 0    | 반환할 첫 번째 결과의 인덱스입니다.
**orderby** |   텍스트 문자열 | 아니요<br>기본값: 확률 감소 | 엔터티 정렬에 사용되는 특성의 이름입니다. 오름차순/내림차순을 선택적으로 지정할 수 있습니다. 형식은 *name:asc* 또는 *name:desc*입니다.
  
 <br>
## <a name="response-json"></a>응답(JSON)
Name | 설명
-------|-----   
**expr** |  요청의 *expr* 매개 변수입니다.
**entities** |  쿼리 식과 일치하는 0개 이상의 엔터티 배열입니다. 각 엔터티에는 자연 로그 확률 값 및 다른 요청된 특성의 값이 포함됩니다.
**aborted** | 요청 시간이 초과되면 true입니다.

<br>
#### <a name="example"></a>예제:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>일반적으로 식은 응답에서 **interpret** 메서드로 얻어집니다.  하지만 쿼리 식을 직접 구성할 수도 있습니다([쿼리 식 구문](QueryExpressionSyntax.md) 참조).  
  
*count* 및 *offset* 매개 변수를 사용하면 대규모 응답(속도는 느릴 수 있음)을 발생시키는 단일 요청을 보내지 않고 많은 수의 결과를 얻을 수 있습니다.  이 예에서 요청은 **interpret** API 응답의 첫 번째 해석에 대한 식을 *expr* 값으로 사용했습니다. *count=2* 매개 변수는 2개의 엔터티 결과가 요청되도록 지정합니다. 그리고 *attributes=Ti,Y,CC,AA.AuN,AA.AuId* 매개 변수는 각 결과에 대해 제목, 연도, 인용 횟수, 작성자 이름 및 작성자 ID가 요청됨을 나타냅니다.  특성 목록은 [엔터티 특성](EntityAttributes.md)을 참조하세요.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
