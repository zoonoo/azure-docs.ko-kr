---
title: 'Interpret 메서드: Knowledge Exploration Service API'
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API에서 Interpret 메서드를 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870931"
---
# <a name="interpret-method"></a>interpret 메서드

*interpret* 메서드는 자연어 쿼리 문자열을 사용하고 문법 및 인덱스 데이터를 기반으로 사용자 의도에 대한 형식이 지정된 해석을 반환합니다.  대화형 검색 경험을 제공하기 위해 이 메서드는 추천 자동 완성을 사용할 수 있도록 *complete* 매개 변수를 1로 설정하면 사용자가 입력하는 각 문자로 호출될 수 있습니다.

## <a name="request"></a>요청

`http://<host>/interpret?query=<query>[&<options>]`

Name|값| 설명
----|----|----
쿼리    | 텍스트 문자열 | 사용자가 입력한 쿼리입니다.  complete를 1로 설정하면 쿼리는 쿼리 추천 자동 완성을 생성하기 위한 접두사로 해석됩니다.        
complete | 0(기본값) 또는 1 | 1은 문법 및 인덱스 데이터를 기반으로 추천 자동 완성이 생성된다는 의미입니다.         
count    | 숫자(기본값=10) | 반환할 최대 해석 수.         
offset   | 숫자(기본값=0) | 반환할 첫 번째 해석의 인덱스입니다.  예를 들어 *count=2&offset=0*은 해석 0 및 1을 반환합니다. *count=2&offset=2*는 해석 2 및 3을 반환합니다.       
시간 제한  | 숫자(기본값=1000) | 시간 제한(밀리초)입니다. 시간 제한이 경과되기 전에 찾은 해석만 반환됩니다.

*count* 및 *offset* 매개 변수를 사용하여 여러 요청에 대한 더 많은 수의 결과를 증분 방식으로 얻을 수 있습니다.

## <a name="response-json"></a>응답(JSON)

JSONPath     | 설명
---------|---------
$.query |요청의 *query* 매개 변수입니다.
$.interpretations   |입력 쿼리가 문법에 일치하는 0개 이상 방법의 배열입니다.
$.interpretations[\*].logprob   |해석의 상대적인 로그 확률입니다(<= 0).  값이 클수록 확률이 높습니다.
$.interpretations[\*].parse |쿼리의 각 부분을 해석하는 방법을 보여주는 XML 문자열입니다.
$.interpretations[\*].rules |문법에 정의된 하나 이상의 규칙 배열로, 해석하는 동안 호출됩니다.
$.interpretations[\*].rules[\*].name    |규칙의 이름입니다.
$.interpretations[\*].rules[\*].output  |규칙의 의미 체계 출력입니다.
$.interpretations[\*].rules[\*].output.type |의미 체계 출력의 데이터 형식입니다.
$.interpretations[\*].rules[\*].output.value|의미 체계 출력의 값입니다.  
$.aborted | 요청 시간이 초과되면 true입니다.

### <a name="parse-xml"></a>구문 분석 XML

구문 분석 XML은 문법의 규칙 및 인덱스의 특성에 대해 일치하는 방법에 관한 정보가 포함된 (완료된) 쿼리를 주석 처리합니다.  다음은 학술 저서 도메인의 한 예입니다.

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

`<rule>` 요소는 `name` 특성에 의해 지정된 규칙과 일치하는 쿼리에서 범위를 구분합니다.  구문 분석이 문법의 규칙 참조를 포함하는 경우에 중첩될 수 있습니다.

`<attr>` 요소는 `name` 특성에 의해 지정된 인덱스 특성과 일치하는 쿼리에서 범위를 구분합니다.  일치에 입력 쿼리의 동의어가 포함되는 경우 `canonical` 특성은 인덱스의 동의어와 일치하는 Canonical을 포함합니다.

## <a name="example"></a>예

학술 저서 예제에서 다음 요청은 “papers by jaime” 접두사 쿼리에 대해 최대 2개의 추천 자동 완성을 반환합니다.

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

응답에는 “papers by jaime” 부분 쿼리를 완성하는 맨 위의 두 가지(“count = 2”) 가장 가능성이 높은 해석(“papers by jaime teevan” 및 “papers by jaime green”)이 포함됩니다.  요청이 “complete=1”을 지정하였으므로 서비스는 작가 “jaime”에 정확히 일치하는 것을 고려하는 대신 쿼리 완성을 생성했습니다. 구문 분석에서 표시된 것처럼 “jamie green” 동의어를 통해 Canonical 값 “j l green”이 일치하였음을 확인합니다.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

의미 체계 출력의 형식이 “query”이면, 이 예제와 같이 *output.value*를 *expr* 매개 변수를 통한 API [*평가*](evaluateMethod.md)에 전달하여 일치하는 개체를 검색할 수 있습니다.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
