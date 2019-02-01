---
title: CalcHistogram 메서드 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: CalcHistogram 메서드를 사용하여 논문 엔터티 집합의 특성 값 분포를 계산합니다.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: cb47b3933ecb6e38aa7945ac7f81f7602a0c8034
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192539"
---
# <a name="calchistogram-method"></a>CalcHistogram 메서드

**calchistogram** REST API는 논문 엔터티 집합의 특성 값 분포를 계산하는 데 사용됩니다.          


**REST 엔드포인트:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>요청 매개 변수

Name  |값 | Required?  |설명
-----------|----------|--------|----------
**expr**    |텍스트 문자열 | 예  |히스토그램을 계산할 엔터티를 지정하는 쿼리 식입니다.
**model** |텍스트 문자열 | 아니요 |쿼리할 모델 이름을 선택합니다.  현재 기본값은 *최신*입니다.
**attributes** | 텍스트 문자열 | 아니요<br>기본값: | 응답에 포함된 특성 값을 지정하는 쉼표로 구분된 목록입니다. 특성 이름은 대/소문자를 구분합니다.
**count** |Number | 아니요<br>Default: 10 |반환할 결과의 수입니다.
**offset**  |Number | 아니요<br>Default: 0 |반환할 첫 번째 결과의 인덱스입니다.
**timeout**  |Number | 아니요<br>Default: 1000 |시간 제한(밀리초)입니다. 시간 제한이 경과되기 전에 찾은 해석만 반환됩니다.

## <a name="response-json"></a>응답(JSON)

Name | 설명
--------|---------
**expr**  |요청의 expr 매개 변수입니다.
**num_entities** | 일치하는 항목의 총 수입니다.
**histograms** |  요청에 지정된 각 특성에 대한 히스토그램 배열입니다.
**histograms[x].attribute** | 이 특성 이름에 대해 히스토그램이 계산되었습니다.
**histograms[x].distinct_values** | 이 특성의 일치하는 엔터티 중에서 고유한 값의 수입니다.
**histograms[x].total_count** | 이 특성의 일치하는 엔터티 중에서 값 인스턴스의 총 수입니다.
**histograms[x].histogram** | 이 특성에 대한 히스토그램 데이터입니다.
**histograms[x].histogram[y].value** |  특성의 값입니다.
**histograms[x].histogram[y].logprob**  |이 특성 값과 일치하는 엔터티의 총 자연 로그 확률입니다.
**histograms[x].histogram[y].count**  |이 특성 값과 일치하는 엔터티의 수입니다.
**aborted** | 요청 시간이 초과되면 true입니다.


#### <a name="example"></a>예제:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>이 예제에서 2010년 이후 특정 작성자의 연도별 게시물 수 히스토그램을 생성하려면 먼저 **interpret** API와 함께 쿼리 문자열 *papers by jaime teevan after 2012*를 사용하여 쿼리 식을 생성할 수 있습니다.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>interpret API에서 반환되는 첫 번째 해석의 언어 식은 *And(Composite(AA.AuN=='jaime teevan'),Y>2012)* 입니다.
<br>이 언어 식 값은 **calchistogram** API로 전달됩니다. *attributes=Y,F.FN* 매개 변수는 논문 수의 분포가 연구의 연도 및 필드여야 함을 나타냅니다.
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>이 요청에 대한 응답은 쿼리 식과 일치하는 논문이 37개 있음을 나타냅니다.  *Year* 특성의 경우 쿼리에 지정된 것처럼 2012년 후 매년 하나씩 총 3개의 고유한 값이 있습니다(즉, 2013, 2014 및 2015).  고유 값 3개의 총 논문 수는 37개입니다.  각 *Year*에 대해 히스토그램은 값, 총 자연 로그 확률 및 일치하는 엔터티 수를 보여줍니다.     

*연구 분야*에 대한 히스토그램은 고유 연구 분야 34개가 있음을 보여줍니다. 한 논문이 여러 연구 분야와 연결될 수 있으므로 총 수(53)는 일치하는 항목 수보다 많을 수 있습니다.  고유 값이 34개 있지만, *count=4* 매개 변수 때문에 상위 4개만 응답에 포함됩니다.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
