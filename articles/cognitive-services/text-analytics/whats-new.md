---
title: 텍스트 분석 API의 새로운 기능
titlesuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics 서비스를 사용 하 여 새로운 개발에 대해 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499954"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API의 새로운 기능

텍스트 분석 API는 지속적으로 업데이트 됩니다. 최신 개발을 최신 상태로 유지 하기 위해이 문서에서는 새로운 릴리스 및 기능에 대 한 정보를 제공 합니다.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>명명 된 엔터티 인식 v3 공개 미리 보기-10 월 2019

이제 NER (명명 된 엔터티 인식)의 다음 버전을 공개 미리 보기에서 사용할 수 있으며, 텍스트에 있는 엔터티의 확장 된 검색 및 분류를 제공 합니다. 다음과 같은 것을 제공합니다.

* 다음과 같은 새로운 엔터티 유형을 인식 합니다.
    * 전화 번호
    * IP 주소

* 개인 정보 엔터티 형식을 인식 하기 위한 새 끝점 (영어만 해당)
* 엔터티 인식 및 엔터티 링크를 위한 별도의 끝점.

엔터티 연결은 영어와 스페인어를 지원 합니다. NER 언어 지원은 엔터티 형식에 따라 달라 집니다. 자세한 내용은 아래 링크를 참조 하세요. 

> [!div class="nextstepaction"]
> [명명 된 엔터티 인식 v3에 대해 자세히 알아보기](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>감정 분석 v3 공개 미리 보기-10 월 2019

이제 감정 분석의 다음 버전을 공개 미리 보기에서 사용할 수 있으며, API의 텍스트 분류 및 점수 매기기의 정확성과 세부 정보에 상당한 향상 된 기능을 제공 합니다. 추가로 다음을 제공 합니다.

* 텍스트의 다른 정서에 대 한 자동 레이블 지정
* 문서 및 문장 수준에서 분석 및 출력을 감정 합니다. 

영어 (`en`), 일본어 (`ja`), 중국어 간체 (`zh-Hans`), 중국어 번체 (`zh-Hant`), 프랑스어 (`fr`), 이탈리아어 (`it`), 스페인어 (`es`), 네덜란드어 (`nl`), 포르투갈어 (`pt`) 및 독일어 (`de`)를 지원 합니다. 다음 지역에서 사용할 수 있습니다. `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, `West US 2`.

> [!div class="nextstepaction"]
> [감정 분석 v 3에 대 한 자세한 정보](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>다음 단계

* [Text Analytics API란?](overview.md)  
* [사용자 시나리오 예제](text-analytics-user-scenarios.md)
* [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [언어 감지](how-tos/text-analytics-how-to-language-detection.md)
* [엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md)
* [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)
