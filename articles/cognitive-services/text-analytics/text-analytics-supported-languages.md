---
title: Text Analytics API에서 지원되는 언어 - Azure Cognitive Services | Microsoft Docs
description: Text Analytics API 작업을 위한 일반 공급 및 미리 보기 언어 지원 목록입니다. 감정 분석, 핵심 구 추출 및 언어 감지에 적용됩니다.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377807"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Text Analytics API에서 지원되는 언어

이 문서에서는 감성 분석, 핵심 구 추출 및 언어 감지를 비롯한 각 작업에 대해 지원되는 언어에 대해 설명합니다.

## <a name="language-detection"></a>언어 검색

Text Analytics API는 최대 120개의 다양한 언어를 감지할 수 있습니다. 언어 감지는 언어의 "스크립트"를 반환합니다. 예를 들어, "I have a dog" 구에 대해서는 `en-US` 대신 `en`을 반환합니다. 유일한 특수 사례는 중국어로, 해당 언어 감지 기능은 제공된 텍스트가 지정된 스크립트를 확인할 수 있는 경우 `zh_CHS` 또는 `zh_CHT`를 반환합니다. 중국어 문서에 대해 특정 스크립트를 식별할 수 없는 경우 간단히 `zh`가 반환됩니다.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>감정 분석, 핵심 구 추출 및 엔터티 링크

감정 분석, 핵심 구 추출 및 엔터티 링크의 경우, 추가 언어의 언어 규칙을 수용하도록 분석기가 미세 조정되므로 지원되는 언어 목록의 선택 범위가 좀 더 구체화됩니다.

## <a name="language-list-and-status"></a>언어 목록 및 상태

언어 지원은 처음에는 미리 보기로 롤아웃되었다가 Text Analytics 서비스 전체의 지원 방식과 관계없이, 다른 언어와 별개로 GA(일반 공급) 상태로 서서히 진행됩니다. Text Analytics API가 일반 공급으로 전환되더라도 언어는 미리 보기 상태를 유지할 수 있습니다.

| 언어    | 언어 코드 | 데이터 | 키 구 | 엔터티 연결 |   메모  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| 덴마크어      | `da`          | ✔ \*     | ✔           |             |     |
| 네덜란드어       | `nl`          | ✔ \*     | ✔          |             |     |
| 영어     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| 핀란드어     | `fi`          | ✔ \*     | ✔           |             |     |
| 프랑스어      | `fr`          | ✔        | ✔           |             |     |
| 독일어      | `de`          | ✔ \*     | ✔           |            |     |
| 그리스어       | `el`          | ✔ \*     |             |            |     |
| 이탈리아어     | `it`          | ✔ \*     | ✔           |             |     |
| 일본어    | `ja`          |          | ✔           |            |     |
| 한국어      | `ko`          |          | ✔           |            |     |
| 노르웨이어(복말) | `no`          | ✔ \*     |  ✔          |             |     |
| 폴란드어      | `pl`          | ✔ \*     |  ✔          |             |     |
| 포르투갈어(포르투갈) | `pt-PT`| ✔        |  ✔          |       |`pt`도 허용됨|
| 포르투갈어(브라질)   | `pt-BR`|          |  ✔   |         |     |
| 러시아어     | `ru`          | ✔ \*     | ✔           |             |     |
| 스페인어     | `es`          | ✔        | ✔           |     |     |
| 스웨덴어     | `sv`          | ✔ \*     | ✔           |             |     |
| 터키어     | `tr`          | ✔ \*     |             |             |     |

\*는 언어 지원이 미리 보기 상태임을 나타냅니다.

## <a name="see-also"></a>참고 항목

[Cognitive Services 설명서 페이지](https://docs.microsoft.com/azure/cognitive-services/)   
[Cognitive Services 제품 페이지](https://azure.microsoft.com/services/cognitive-services/)
