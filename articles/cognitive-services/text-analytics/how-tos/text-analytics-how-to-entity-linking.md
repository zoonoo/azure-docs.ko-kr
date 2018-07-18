---
title: 텍스트 분석 REST API(Azure의 Microsoft Cognitive Services)에서 엔터티 링크 설정을 사용하는 방법 | Microsoft Docs
description: 이 연습 자습서에서는 Azure의 Microsoft Cognitive Services에서 텍스트 분석 REST API를 사용하여 엔터티를 식별하고 확인하는 방법을 알아봅니다.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374511"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>텍스트 분석(미리 보기)에서 연결된 엔터티를 식별하는 방법

[엔터티 링크 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)는 구조화되지 않은 텍스트를 사용하고, 각 JSON 문서에 대해 웹의 추가 정보 링크(Wikipedia 및 Bing)가 있는 명확히 구분된 엔터티 목록을 반환합니다. 

## <a name="entity-linking-vs-named-entity-recognition"></a>엔터티 링크 설정 및 명명된 엔터티 인식

자연어 처리에서 엔터티 링크 설정 및 NER(명명된 엔터티 인식)의 개념을 쉽게 혼동할 수 있습니다. 텍스트 분석 `entities` 끝점의 미리 보기 버전에서는 엔터티 링크 설정만 지원됩니다.

엔터티 링크 설정은 텍스트에서 찾은 엔터티의 ID를 식별하고 명확하게 구분하는 기능입니다(예: “Mars”가 행성 또는 로마의 전쟁의 신으로 사용되고 있는지 확인). 이 프로세스를 수행하려면 인식된 엔터티가 연결되는 기술 자료가 있어야 합니다. Wikipedia가 `entities` 끝점 텍스트 분석의 기술 자료로 사용됩니다.

### <a name="language-support"></a>언어 지원

여러 언어로 엔터티 링크 설정을 사용하려면 각 언어에서 해당하는 기술 자료를 사용해야 합니다. 텍스트 분석의 엔터티 링크 설정에서 이는 `entities` 끝점에서 지원하는 각 언어가 해당 언어의 Wikipedia 자료에 연결됨을 의미합니다. 자료 크기는 언어마다 다르기 때문에 엔터티 링크 설정 기능의 재현율도 각기 다릅니다.


## <a name="preparation"></a>준비

id, text, language 형식의 JSON 문서가 있어야 합니다.

현재 지원되는 언어는 [이 목록](../text-analytics-supported-languages.md)을 참조하세요.

문서 크기는 문서당 5,000자 이하여야 하며, 컬렉션당 최대 1,000개 항목(ID)을 포함할 수 있습니다. 컬렉션은 요청 본문을 통해 제출됩니다. 다음 예제는 엔터티 링크 설정 끝에 제출할 수 있는 콘텐츠에 대한 설명입니다.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대한 세부 정보는 [텍스트 분석 API를 호출하는 방법](text-analytics-how-to-call-api.md)에서 확인할 수 있습니다. 다음 사항은 편의를 위해 다시 설명합니다.

+ **POST** 요청을 만듭니다. 이 요청에 대한 API 문서인 [엔터티 링크 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)를 검토합니다.

+ 핵심 구 추출에 대한 HTTP 끝점을 설정합니다. `/entities` 리소스인 `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`를 포함해야 합니다.

+ 텍스트 분석 작업에 대한 액세스 키를 포함하도록 요청 헤더를 설정합니다. 자세한 내용은 [끝점 및 액세스 키를 찾는 방법](text-analytics-how-to-access-key.md)을 참조하세요.

+ 요청 본문에 이 분석을 위해 준비한 JSON 문서 컬렉션을 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나, [문서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)에서 **API 테스트 콘솔**을 열어 요청을 구성하고 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 게시

요청을 받으면 분석이 수행됩니다. 서비스는 분당 최대 100개의 요청을 허용합니다. 각 요청은 최대 1MB까지 가능합니다.

서비스가 상태 비저장인 것에 유의하세요. 계정에 아무 데이터도 저장되지 않습니다. 결과는 응답을 통해 즉시 반환됩니다.

## <a name="step-3-view-results"></a>3단계: 결과 보기

모든 POST 요청은 ID 및 검색된 속성과 함께 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 응용 프로그램에 결과를 스트리밍하거나, 로컬 시스템의 파일에 출력을 저장한 후에 데이터를 정렬, 검색, 조작할 수 있는 응용 프로그램으로 가져올 수 있습니다.

엔터티 링크 설정의 출력 예는 다음과 같습니다.

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

사용 가능한 경우 응답에는 검색된 각 엔터티에 대한 Wikipedia ID, Wikipedia URL 및 Bing ID가 포함됩니다. 연결된 엔터티에 대한 정보와 함께 이러한 정보를 사용하여 응용 프로그램을 더욱 개선할 수 있습니다.


## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 텍스트 분석을 사용하여 엔터티 링크 설정에 대한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

+ [엔터티 링크 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)를 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 id, text 및 language 코드가 포함됩니다.
+ POST 요청은 개인 설정된 [액세스 키와 구독에 유효한 끝점](text-analytics-how-to-access-key.md)을 사용하여 `/entities` 끝점에 대해 수행됩니다.
+ 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 응용 프로그램에서 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목 

 [텍스트 분석 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
 [텍스트 분석 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
