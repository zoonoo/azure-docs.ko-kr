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
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417317"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Text Analytics API에 대한 언어 및 지역 지원

이 문서에서는 각 작업에 대해 지원 되는 언어를 설명: 감정 분석, 핵심 문구 추출, 언어 감지 및 명명 된 엔터티 인식 합니다.

## <a name="language-detection"></a>언어 검색

Text Analytics API는 다양 한 언어, 변형, 언어 및 국가/문화권 일부 언어를 검색할 수 있습니다.  언어 감지는 언어의 "스크립트"를 반환합니다. 예를 들어, "I have a dog" 구에 대해서는 `en-US` 대신 `en`을 반환합니다. 유일한 특수 사례는 중국어로, 해당 언어 감지 기능은 제공된 텍스트가 지정된 스크립트를 확인할 수 있는 경우 `zh_CHS` 또는 `zh_CHT`를 반환합니다. 중국어 문서에 대해 특정 스크립트를 식별할 수 없는 경우 간단히 `zh`가 반환됩니다.

이 기능에 대 한 언어의 정확한 목록을 게시 하지 않습니다 하지만 다양 한 언어, 변형, 언어 및 국가/문화권 일부 언어를 검색할 수 있습니다. 

콘텐츠를 덜 사용 되는 언어로 표현 된 경우 코드를 반환 하는 경우를 확인 하려면 언어 감지를 시도할 수 있습니다. 검색할 수 없는 언어에 대 한 응답은 `unknown`합니다.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>감정 분석, 핵심 구 추출 및 명명 된 엔터티 인식

감정 분석, 핵심 구 추출 및 엔터티 인식의 경우, 추가 언어의 언어 규칙을 수용하도록 분석기가 미세 조정되므로 지원되는 언어 목록의 선택 범위가 좀 더 구체화됩니다.

## <a name="language-list-and-status"></a>언어 목록 및 상태

언어 지원은 처음에는 미리 보기로 롤아웃되었다가 Text Analytics 서비스 전체의 지원 방식과 관계없이, 다른 언어와 별개로 GA(일반 공급) 상태로 서서히 진행됩니다. Text Analytics API가 일반 공급으로 전환되더라도 언어는 미리 보기 상태를 유지할 수 있습니다.

| 언어    | 언어 코드 | 데이터 | 키 구 | 명명된 엔터티 인식 |   메모  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| 아랍어      | `ar`          |           |             | ✔ \*                     | |
| 체코어       | `cs`          |           |             | ✔ \*                     | |
| 중국어-간체 | `zh-CN`|           |             | ✔ \*        |    |
| 덴마크어      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| 네덜란드어       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| 영어     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| 핀란드어     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 프랑스어      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| 독일어      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| 그리스어       | `el`          | ✔ \*     |             |            |     |
| 헝가리어   | `hu`          |           |             |  ✔ \*          |     | 
| 이탈리아어     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 일본어    | `ja`          |          | ✔           |  ✔ \*          |     |
| 한국어      | `ko`          |          | ✔           |  ✔ \*          |     |
| 노르웨이어(복말) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| 폴란드어      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| 포르투갈어(포르투갈) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`도 허용됨|
| 포르투갈어(브라질)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| 러시아어     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 스페인어     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| 스웨덴어     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| 터키어     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* 언어 지원은 미리 보기로 제공

\*\* 라는 엔터티 인식 하 고 [Entity linking](how-tos/text-analytics-how-to-entity-linking.md) 모두가이 언어에 사용할 수 있습니다.    

## <a name="see-also"></a>참고 항목

[Cognitive Services 설명서 페이지](https://docs.microsoft.com/azure/cognitive-services/)   
[Cognitive Services 제품 페이지](https://azure.microsoft.com/services/cognitive-services/)
