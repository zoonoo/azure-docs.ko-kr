---
title: 텍스트 분석 API의 v2 마이그레이션 가이드
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 API 버전 3을 사용 하도록 응용 프로그램을 이동 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 416ef4ceddbb43e9f1606d44a66ffd5295cee4e6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699898"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>텍스트 분석 API 버전 3(sp3)으로 마이그레이션

텍스트 분석 API 버전 2.1을 사용 하는 경우이 문서를 참조 하 여 버전 3.x를 사용 하도록 응용 프로그램을 업그레이드할 수 있습니다. 버전 3.0은 일반적으로 사용할 수 있으며 확장 [된 명명 된 엔터티 인식 (NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) 및 [모델 버전 관리](concepts/model-versioning.md)와 같은 새로운 기능을 소개 합니다. 또한 평가판 [마이닝](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)과 같은 기능을 추가 하는 v 3.1 (v 3.1-preview. x)의 미리 보기 버전을 사용할 수 있습니다. V2에서 사용 된 모델은 향후 업데이트를 받지 않습니다. 

## <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment-analysis)

### <a name="feature-changes"></a>기능 변경 

2.1 버전의 감정 분석는 API에 전송 된 각 문서에 대해 0과 1 사이의 감정 점수를 반환 합니다. 점수가 1에 가까울수록 감정가 더 많이 표시 됩니다. 버전 3에서는 문장과 문서 전체에 대해 "긍정" 또는 "음수"와 같은 감정 레이블을 대신 반환 합니다. 

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 감정 분석을 위해 해당 요청 끝점을 v3 끝점으로 업데이트 합니다. 예를 들면와 같습니다 `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . [API의 응답](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)에서 반환 된 감정 레이블을 사용 하도록 응용 프로그램을 업데이트 해야 할 수도 있습니다. 

JSON 응답의 예제는 참조 설명서를 참조 하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [버전 3.1 미리 보기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER 및 엔터티 연결](#tab/named-entity-recognition)

### <a name="feature-changes"></a>기능 변경

버전 2.1에서 텍스트 분석 API는 NER (명명 된 엔터티 인식) 및 엔터티 연결에 대해 하나의 끝점을 사용 합니다. 버전 3은 확장 된 명명 된 엔터티 검색을 제공 하 고 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용 합니다. V 3.1-preview. 1부터 NER는 개인 `pii` 정보 및 상태 정보를 추가로 검색할 수 있습니다. `phi` 

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 해당 요청 끝점을 NER 및/또는 엔터티 연결에 대 한 v3 끝점으로 업데이트 합니다.

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[API의 응답](how-tos/text-analytics-how-to-entity-linking.md#view-results)에 반환 된 [엔터티 범주](named-entity-types.md) 를 사용 하도록 응용 프로그램을 업데이트 해야 할 수도 있습니다.

JSON 응답의 예제는 참조 설명서를 참조 하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [버전 3.1 미리 보기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>버전 2.1 엔터티 범주

다음 표에는 NER v 2.1에 대해 반환 된 엔터티 범주가 나와 있습니다.

| 범주   | Description                          |
|------------|--------------------------------------|
| 사람   |   사람의 이름입니다.  |
|위치    | 자연 스러운 랜드마크, 구조, 지리적 기능 및 지정 학적 엔터티 |
|조직 | 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다. |
| PhoneNumber | 전화 번호 (미국과 EU 전화 번호만 해당). |
| Email | 전자 메일 주소. |
| URL | 웹 사이트에 대 한 Url입니다. |
| IP | 네트워크 IP 주소. |
| DateTime | 날짜 및 시간입니다.| 
| Date | 일정 날짜 |
| 시간 | 하루 중 시간 |
| DateRange | 날짜 범위. |
| TimeRange | 시간 범위. |
| Duration | 기간 |
| 설정 | 반복 횟수를 설정 합니다. |
| 수량 | 숫자 및 숫자 수량. |
| 숫자 | 숫자 |
| 백분율 | 백분율.|
| 서수 | 서 수 번호입니다. |
| 나이 | 에이징을. |
| 통화 | 환산. |
| 차원 | 차원과 측정. |
| 온도 | 낮게. |

## <a name="language-detection"></a>[언어 감지](#tab/language-detection)

### <a name="feature-changes"></a>기능 변경 

V3에서 언어 검색 기능 출력이 변경 되었습니다. JSON 응답은 대신을 포함 합니다 `ConfidenceScore` `score` . 또한 V3은  `detectedLanguage` 각 문서에 대 한 특성에서 하나의 언어만 반환 합니다.

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 언어 검색을 위해 해당 요청 끝점을 v3 끝점으로 업데이트 합니다. 예를 들면와 같습니다 `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . `ConfidenceScore`API의 응답 대신을 사용 하도록 응용 프로그램을 업데이트 해야 할 수도 있습니다 `score` . [](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results) 

JSON 응답의 예제는 참조 설명서를 참조 하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>기능 변경 

키 구 추출 기능이 끝점 버전 외부의 v3에서 변경 되지 않았습니다.

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 키 구 추출을 위해 요청 끝점을 v3 끝점으로 업데이트 합니다. 예: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

JSON 응답의 예제는 참조 설명서를 참조 하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>참고 항목

* [Text Analytics API란?](overview.md)
* [언어 지원](language-support.md)
* [모델 버전 관리](concepts/model-versioning.md)
