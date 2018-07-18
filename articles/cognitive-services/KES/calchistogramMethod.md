---
title: Knowledge Exploration Service API의 CalcHistogram 메서드 | Microsoft Docs
description: Cognitive Services의 KES(Knowledge Exploration Service) API에서 CalcHistogram 메서드를 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373062"
---
# <a name="calchistogram-method"></a>calchistogram 메서드
*calchistogram* 메서드는 구조화된 쿼리 식과 일치하는 개체를 계산하고 해당 특성 값의 분포를 계산합니다.

## <a name="request"></a>요청
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name|값|설명
----|-----|-----------
expr | 텍스트 문자열 | 히스토그램을 계산할 인덱스 엔터티를 지정하는 구조화된 쿼리 식입니다.
특성 | 텍스트 문자열(기본값="") | 응답에 포함되는 쉼표로 구분된 특성 목록입니다.
count   | 숫자(기본값=10) | 반환할 결과의 수입니다.
offset  | 숫자(기본값=0) | 반환할 첫 번째 결과의 인덱스입니다.

## <a name="response-json"></a>응답(JSON)
JSONPath | 설명
----|----
$.expr | 요청의 *expr* 매개 변수입니다.
$.num_entities | 일치하는 항목의 총 수입니다.
$.histograms |  요청된 각 특성에 대한 히스토그램 배열입니다.
$.histograms[\*].attribute | 이 특성 이름에 대해 히스토그램이 계산되었습니다.
$.histograms[\*].distinct_values | 이 특성의 일치하는 엔터티 중에서 고유한 값의 수입니다.
$.histograms[\*].total_count | 이 특성의 일치하는 엔터티 중에서 값 인스턴스의 총 수입니다.
$.histograms[\*].histogram | 이 특성에 대한 히스토그램 데이터입니다.
$.histograms[\*].histogram[\*].value | 특성 값입니다.
$.histograms[\*].histogram[\*].logprob  | 이 특성 값과 일치하는 엔터티의 총 자연 로그 확률입니다.
$.histograms[\*].histogram[\*].count    | 이 특성 값과 일치하는 엔터티의 수입니다.
$.aborted | 요청 시간이 초과되면 True입니다.

### <a name="example"></a>예
학술 저서 예제에서, 다음은 2013년 이후 특정 작성자에 대한 연도 및 키워드별 게시물 수 히스토그램을 계산합니다.

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

응답은 쿼리 식과 일치하는 논문이 37개 있음을 나타냅니다.  *Year* 특성의 경우 2013년 이후 매년 하나씩 총 3개의 고유한 값이 있습니다.  고유 값 3개의 총 논문 수는 37개입니다.  각 *Year*에 대해 히스토그램은 값, 총 자연 로그 확률 및 일치하는 엔터티 수를 보여줍니다.     

*Keyword*에 대한 히스토그램은 고유한 키워드가 34개 있음을 보여줍니다. 한 논문이 여러 키워드와 연결될 수 있으므로 총 수(53)는 일치하는 항목 수보다 많을 수 있습니다.  고유 값이 34개 있지만, "count=4" 매개 변수 때문에 상위 4개만 응답에 포함됩니다.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
