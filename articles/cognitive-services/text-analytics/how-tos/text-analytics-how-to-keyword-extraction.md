---
title: 텍스트 분석 REST API(Azure의 Microsoft Cognitive Services)에서 핵심 구를 추출하는 방법 | Microsoft Docs
description: 이 연습 자습서에서는 Azure의 Microsoft Cognitive Services에서 텍스트 분석 REST API를 사용하여 핵심 구를 추출하는 방법을 알아봅니다.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: bbca745da1fe657c1316d9e4e5fbeeeabfa5e1ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216747"
---
# <a name="example-how-to-extract-key-phrases-in-text-analytics"></a>예제: Text Analytics에서 핵심 구를 추출하는 방법

[핵심 구 추출 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)는 구조화되지 않은 텍스트를 평가하고 각 JSON 문서에 대해 핵심 구 목록을 반환합니다. 

이 기능은 문서 컬렉션에서 주요 지점을 빠르게 식별해야 하는 경우에 유용합니다. 예를 들어 "음식이 맛있고 훌륭한 직원이 있었습니다"라는 입력 텍스트가 제공되면 서비스에서 "음식" 및 "훌륭한 직원"이라는 핵심 발화 지점을 반환합니다.

현재 핵심 구 추출은 영어, 독일어, 스페인어 및 일본어를 지원합니다. 다른 언어는 미리 보기로 있습니다. 자세한 내용은 [지원되는 언어](../text-analytics-supported-languages.md)를 참조하세요.

> [!TIP]
> Text Analytics는 핵심 구 추출을 위한 Linux 기반 Docker 컨테이너 이미지도 제공하므로 데이터와 가까이 [Text Analytics 컨테이너를 설치하고 실행](text-analytics-how-to-install-containers.md)할 수 있습니다.

## <a name="preparation"></a>준비

핵심 구 추출은 사용할 텍스트를 더 큰 청크로 분할하여 제공할 때 가장 적합합니다. 이는 작은 텍스트 블록에서 더 나은 성능을 수행하는 감정 분석과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

id, text, language 형식의 JSON 문서가 있어야 합니다.

문서 크기는 문서당 5,000자 미만이어야 하며, 컬렉션당 최대 1,000개의 항목(ID)을 포함할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다. 다음 예제는 핵심 구 추출에 제출할 수 있는 콘텐츠에 대한 설명입니다.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대한 자세한 내용은 [텍스트 분석 API를 호출하는 방법](text-analytics-how-to-call-api.md)에서 찾을 수 있습니다. 편의상 다음 사항을 다시 설명합니다.

+ **POST** 요청을 만듭니다. 이 요청에 대한 API 문서인 [핵심 문구 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용하여 핵심 구 추출을 위한 HTTP 엔드포인트를 설정합니다. `/keyPhrases` 리소스를 포함해야 합니다(예: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`).

+ Text Analytics 작업에 대한 액세스 키가 포함되도록 요청 헤더를 설정합니다. 자세한 내용은 [엔드포인트 및 액세스 키를 찾는 방법](text-analytics-how-to-access-key.md)을 참조하세요.

+ 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나 [설명서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)에서 **API 테스트 콘솔**을 열어 요청을 구성하고, 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 게시

요청을 받으면 분석이 수행됩니다. 서비스에서는 분당 최대 100개의 요청을 허용합니다. 각 요청은 최대 1MB가 될 수 있습니다.

상태 비저장 서비스임에 유의하세요. 계정에는 아무 데이터도 저장되지 않습니다. 결과는 응답으로 즉시 반환됩니다.

## <a name="step-3-view-results"></a>3단계: 결과 보기

모든 POST 요청에서는 ID 및 감지된 속성이 있는 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다.

핵심 구 추출에 대한 출력 예제는 다음과 같습니다.

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

앞에서 설명한 대로, 분석기에서 필요하지 않은 단어는 찾아서 버리고, 문장의 주체 또는 객체로 보이는 단일 용어 또는 구는 유지합니다. 

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 Text Analytics를 사용하여 핵심 구 추출에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

+ [핵심 구 추출 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)는 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 id, text 및 language 코드가 포함됩니다.
+ POST 요청은 구독에 유효한 개인 설정 [액세스 키와 엔드포인트](text-analytics-how-to-access-key.md)를 사용하여 `/keyphrases` 엔드포인트에 수행되는 요청입니다.
+ 각 문서 ID에 대한 핵심 단어와 구로 구성된 응답 출력은 Excel 및 Power BI를 포함하여 JSON을 허용하는 모든 앱으로 스트림할 수 있습니다.

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
