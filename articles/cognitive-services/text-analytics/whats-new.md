---
title: 텍스트 분석 API의 새로운 내용
titleSuffix: Text Analytics - Azure Cognitive Services
description: 이 문서에서는 Azure 인지 서비스 텍스트 분석에 대한 새 릴리스 및 기능에 대한 정보를 제공합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188796"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API의 새로운 기능

텍스트 분석 API는 지속적으로 업데이트됩니다. 이 문서에서는 최신 정보를 최신 상태로 유지하기 위해 새 릴리스 및 기능에 대한 정보를 제공합니다.

## <a name="february-2020"></a>2020년 2월

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>텍스트 분석 API v3 공개 미리 보기에 대한 SDK 지원

통합 Azure [SDK 릴리스의](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)일부로 텍스트 분석 API v3 SDK는 이제 다음 프로그래밍 언어에 대한 공개 미리 보기로 사용할 수 있습니다.
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript(Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [텍스트 분석 API v3 SDK에 대해 자세히 알아보기](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>명명된 엔터티 인식 v3 공개 미리 보기

텍스트에 있는 일반 및 개인 정보 엔터티의 검색을 확장하면서 명명된 엔터티 인식(NER) v3 공개 미리 보기 서비스에서 추가 엔터티 유형을 사용할 수 있습니다. 이 업데이트에서는 [다음을](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`포함하는 모델 버전을 소개합니다.

* 다음과 같은 일반 엔터티 유형 인식(영어만 해당)
    * 인물 유형
    * Product
    * 행사
    * 위치 하의 하위 유형으로 지정학적 엔터티(GPE)
    * 기술

* 다음과 같은 개인 정보 주체 유형(영어만 해당)의 인식:
    * Person
    * 조직
    * 수량에 따라 하위 유형으로 나이
    * DateTime에서 하위 유형으로 날짜 지정
    * Email 
    * 전화 번호(미국만)
    * URL
    * IP 주소

> [!div class="nextstepaction"]
> [명명된 엔터티 인식 v3에 대해 자세히 알아보기](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019년 10월

#### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

* 개인 정보 엔터티 유형을 인식하기 위한 [새로운 끝점(영어만](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) 해당)

* [엔터티 인식](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) 및 [엔터티 연결에](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)대한 별도의 끝점.

* [모델 버전](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`:
    * 텍스트에 있는 엔터티의 확장된 검색 및 분류. 
    * 다음과 같은 새 엔터티 유형 인식:
        * 전화 번호
        * IP 주소

엔터티 연결은 영어와 스페인어를 지원합니다. NER 언어 지원은 엔터티 유형에 따라 다릅니다.

#### <a name="sentiment-analysis-v3-public-preview"></a>감정 분석 v3 공개 미리 보기

* 감정을 분석하기 위한 [새로운 끝점입니다.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)
* [모델 버전](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`:

    * API의 텍스트 분류 및 점수 매기기의 정확성과 세부 사항이 크게 향상되었습니다.
    * 텍스트의 다양한 감정에 대한 자동 레이블 지정.
    * 문서 및 문장 수준에서 감정 분석 및 출력. 

그것은 영어`en`(),`ja`일본어 (),`zh-Hans`중국어 간체`zh-Hant`(),`fr`중국어`it`번체`es`(),`nl`중국어 번체`pt`(),`de`프랑스어 (), 이탈리아어 ( `Australia East` `Central Canada`), `Central US` `East Asia`스페인어 `East US` `East US 2`(), 독일어 (), 포르투갈어 (), 독일어 (), 및 독일어 (), 다음 지역에서 사용할 수 있습니다 : , `North Europe`, , `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2`, , , , , , , , , , , , , , , , , , , , , , 

> [!div class="nextstepaction"]
> [감정 분석 v3에 대해 자세히 알아보기](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>다음 단계

* [텍스트 분석 API란?](overview.md)  
* [사용자 시나리오 예](text-analytics-user-scenarios.md)
* [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [언어 감지](how-tos/text-analytics-how-to-language-detection.md)
* [엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md)
* [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)
