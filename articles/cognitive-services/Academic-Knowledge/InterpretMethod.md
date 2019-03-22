---
title: Interpret 메서드 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Interpret 메서드를 사용하여 Microsoft Cognitive Services의 Academic Graph 데이터 및 Academic Grammar를 기반으로 형식이 지정된 사용자 쿼리 문자열 해석을 반환합니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: b679f1da0ada3e61fca79cdb985a43dc445877ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57975494"
---
# <a name="interpret-method"></a>Interpret 메서드

**interpret** REST API는 최종 사용자 쿼리 문자열(즉, 애플리케이션 사용자가 입력한 쿼리)을 가져와서 Academic Graph 데이터 및 Academic Grammar를 기반으로 형식이 지정된 사용자 의도 해석을 반환합니다.

대화형 경험을 제공하려면 사용자가 입력한 각 문자 뒤에서 이 메서드를 반복적으로 호출하면 됩니다. 이 경우 **complete** 매개 변수를 1로 설정하여 자동 완성 제안을 사용해야 합니다. 애플리케이션에 자동 완성이 필요 없는 경우 **complete** 매개 변수를 0으로 설정해야 합니다.

**REST 엔드포인트:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>요청 매개 변수

이름     | 값 | Required?  | 설명
---------|---------|---------|---------
**query**    | 텍스트 문자열 | 예 | 사용자가 입력한 쿼리입니다.  complete를 1로 설정하면 쿼리는 쿼리 추천 자동 완성을 생성하기 위한 접두사로 해석됩니다.        
**model**    | 텍스트 문자열 | 아닙니다.  | 쿼리할 모델의 이름입니다.  현재 기본값은 *최신*입니다.        
**complete** | 0 또는 1 | 아닙니다.<br>기본값: 0  | 1은 문법 및 그래프 데이터를 기반으로 자동 완성 제안이 생성된다는 의미입니다.         
**count**    | Number | 아닙니다.<br>기본값:10 | 반환할 최대 해석 수.         
**offset**   | Number | 아닙니다.<br>기본값: 0  | 반환할 첫 번째 해석의 인덱스입니다. 예를 들어 *count=2&offset=0*은 해석 0 및 1을 반환합니다. *count=2&offset=2*는 해석 2 및 3을 반환합니다.       
**timeout**  | Number | 아닙니다.<br>기본값: 1000 | 시간 제한(밀리초)입니다. 시간 제한이 경과되기 전에 찾은 해석만 반환됩니다.

<br>
  
## <a name="response-json"></a>응답(JSON)

이름     | 설명
---------|---------
**query** |요청의 *query* 매개 변수입니다.
**interpretations** |문법에 따라 사용자 입력을 일치시키는 0개 이상의 서로 다른 방법 배열입니다.
**interpretations[x].logprob**  |해석의 상대 자연 로그 확률입니다. 값이 클수록 확률이 높습니다.
**interpretations[x].parse**  |쿼리의 각 부분을 해석하는 방법을 보여주는 XML 문자열입니다.
**interpretations[x].rules**  |문법에 정의된 1개 이상의 규칙 배열로, 해석하는 동안 호출됩니다. Academic Knowledge API의 경우 항상 1개의 규칙이 있습니다.
**interpretations[x].rules[y].name**  |규칙의 이름입니다.
**interpretations[x].rules[y].output**  |규칙의 출력입니다.
**interpretations[x].rules[y].output.type** |규칙의 출력 데이터 형식입니다.  Academic Knowledge API의 경우 항상 "쿼리"가 있습니다.
**interpretations[x].rules[y].output.value**  |규칙의 출력입니다. Academic Knowledge API의 경우 이것은 evaluate 및 calchistogram 메서드에 전달할 수 있는 쿼리 식 문자열입니다.
**aborted** | 요청 시간이 초과되면 true입니다.

<br>

#### <a name="example"></a>예제:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>아래 응답에는 부분 사용자 입력 *papers by jaime*: *papers by jaime teevan* 및 *papers by jaime green*을 완성하는 가장 가능성이 높은 최상위 해석 2개(매개 변수 *count=2* 때문에)가 포함됩니다.  요청에서 *complete=1*을 지정했기 때문에 서비스에서는 작성자 *jaime*와 정확히 일치하는 항목만 고려하는 대신 쿼리 완성을 생성했습니다. 구문 분석에서 표시된 것처럼 *jamie green* 동의어를 통해 Canonical 값 *j l green*이 일치하였음을 확인합니다.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>해석에 대한 엔터티 결과를 검색하려면 **interpret** API의 *output.value*를 사용하고, *expr* 매개 변수를 통해 **evaluate** API로 전달해야 합니다. 이 예제에서 첫 번째 해석의 쿼리는 다음과 같습니다. 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
