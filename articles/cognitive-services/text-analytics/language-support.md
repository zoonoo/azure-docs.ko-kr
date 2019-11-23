---
title: 언어 지원 - Text Analytics API
titleSuffix: Azure Cognitive Services
description: Text Analytics API에서 지원하는 자연 언어 목록입니다. 이 문서에서는 감정 분석, 핵심 구 추출, 언어 감지 및 엔터티 인식을 비롯한 각 작업에 대해 지원되는 언어에 대해 설명합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 529568cf4f667e72b0b4fb7ba83d0b85c4a90341
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499984"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Text Analytics API에 대한 언어 및 지역 지원

이 문서에서는 각 작업에 대해 지원되는 언어를 설명합니다: 감정 분석, 핵심 문구 추출, 언어 감지 및 명명된 엔터티 인식.

## <a name="language-detection"></a>언어 검색

Text Analytics API는 다양한 언어, 변형, 방언 및 일부 국가/문화권 언어를 감지할 수 있습니다.  언어 감지는 언어의 "스크립트"를 반환합니다. 예를 들어, "I have a dog" 구에 대해서는 `en` 대신 `en-US`을 반환합니다. 유일한 특수 사례는 중국어로, 해당 언어 감지 기능은 제공된 텍스트가 지정된 스크립트를 확인할 수 있는 경우 `zh_CHS` 또는 `zh_CHT`를 반환합니다. 중국어 문서에 대해 특정 스크립트를 식별할 수 없는 경우 간단히 `zh`가 반환됩니다.

이 기능에 대한 정확한 언어 목록은 게시되지 않지만 다양한 언어, 변형, 방언 및 일부 지역/문화권 언어를 검색 할 수 있습니다. 

사용 빈도가 낮은 언어로 표현된 콘텐츠가 있는 경우, 언어 감지를 사용하여 코드가 반환되는지 확인할 수 있습니다. 감지할 수 없는 언어에 대한 응답은 `unknown`입니다.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>감정 분석, 핵심 구 추출 및 명명된 엔터티 인식

감정 분석, 핵심 구 추출 및 엔터티 인식의 경우, 추가 언어의 언어 규칙을 수용하도록 분석기가 미세 조정되므로 지원되는 언어 목록의 선택 범위가 좀 더 구체화됩니다. 명명 된 엔터티 인식 v2에서 전체 [엔터티 형식](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition-v2) 집합에 대 한 지원은 현재 다음 언어로 제한 됩니다. 
* 영어
* 중국어-간체
* 프랑스어
* 독일어
* 스페인어

다른 언어에 대해서는 `Person`, `Location` 및 `Organization` 명명 된 엔터티만 반환 됩니다.

## <a name="language-list-and-status"></a>언어 목록 및 상태

언어 지원은 처음에는 미리 보기로 롤아웃되었다가 Text Analytics 서비스 전체의 지원 방식과 관계없이, 다른 언어와 별개로 GA(일반 공급) 상태로 서서히 진행됩니다. Text Analytics API가 일반 공급으로 전환되더라도 언어는 미리 보기 상태를 유지할 수 있습니다.

> [!NOTE]
> NER (명명 된 엔터티 인식) v3 공개 미리 보기에 대 한 자세한 언어 지원은 [명명 된 엔터티 형식](named-entity-types.md)을 참조 하세요.

| 언어              | 언어 코드 | 감정 | 핵심 문구 | 명명된 엔터티 인식 | 엔터티 연결 |       참고 사항        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| 아랍어                |     `ar`      |           |             |           ✔ \*           |                |                    |
| 체코어                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| 중국어-간체    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| 중국어-번체   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| 덴마크어                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 네덜란드어                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 영어               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| 핀란드어               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 프랑스어                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 독일어                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 그리스어                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| 헝가리어             |     `hu`      |           |             |           ✔ \*           |                |                    |
| 이탈리아어               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 일본어              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 한국어                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| 노르웨이어(복말)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 폴란드어                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 포르투갈어(포르투갈) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt`도 허용됨 |
| 포르투갈어(브라질)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| 러시아어               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 스페인어               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| 스웨덴어               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 터키어               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* 언어 지원은 미리 보기 상태입니다.

\** [감정 분석 v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview) 및/또는 [명명 된 엔터티 인식 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview) 공개 미리 보기 에서도 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목:

[Cognitive Services 설명서 페이지](https://docs.microsoft.com/azure/cognitive-services/)   
[Cognitive Services 제품 페이지](https://azure.microsoft.com/services/cognitive-services/)
