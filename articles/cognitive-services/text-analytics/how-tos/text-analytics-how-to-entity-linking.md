---
title: 텍스트 분석 API에서 엔터티 인식 사용
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API를 사용 하 여 텍스트에 있는 엔터티의 id를 식별 하 고 명확 하 게 구분 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326636"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics에서 명명 된 엔터티 인식을 사용 하는 방법

[명명 된 엔터티 인식 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 는 구조화 되지 않은 텍스트를 사용 하 고, 각 JSON 문서에 대해 웹의 추가 정보에 대 한 링크를 사용 하 여 명확 하 게 표시 되는 엔터티의 목록을 반환 합니다 (위키백과 및 Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>엔터티 링크 및 명명된 엔터티 인식

Text Analytics ' `entities` 끝점은 NER (명명 된 엔터티 인식) 및 엔터티 연결을 모두 지원 합니다.

### <a name="entity-linking"></a>엔터티 연결
엔터티 링크 설정은 텍스트에서 찾은 엔터티의 ID를 식별하고 명확하게 구분하는 기능입니다(예: "Mars"가 행성 또는 로마의 전쟁의 신으로 사용되고 있는지 확인). 이 프로세스를 수행하려면 인식된 엔터티가 연결되는 기술 자료가 있어야 합니다. Wikipedia가 `entities` 엔드포인트 텍스트 분석의 기술 자료로 사용됩니다.

### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)
NER (명명 된 엔터티 인식)는 텍스트에서 다양 한 엔터티를 식별 하 고 미리 정의 된 클래스 또는 형식으로 분류 하는 기능입니다. 

## <a name="named-entity-recognition-v3-public-preview"></a>명명 된 엔터티 인식 v3 공개 미리 보기

이제 명명 된 엔터티 인식의 다음 버전을 공개 미리 보기에서 사용할 수 있습니다. 엔터티 연결 및 명명 된 엔터티 인식 모두에 대 한 업데이트를 제공 합니다. [API 테스트 콘솔](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)을 사용 하 여 사용해 보세요.

:::row:::
    :::column span="":::
        **기능**
    :::column-end:::
    ::: column span="":::
        **설명** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        확장 된 엔터티 형식 및 하위 형식
    :::column-end:::
    :::column span="":::
     여러 명명 된 엔터티 형식에 대 한 분류 및 검색 기능이 확장 되었습니다.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        별도 요청 끝점 
    :::column-end:::
    :::column span="":::
        엔터티 연결 및 NER 요청을 보내기 위한 별도의 끝점입니다.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` 매개 변수
    :::column-end:::
    :::column span="":::
        Text Analytics 모델의 버전을 선택 하는 선택적 매개 변수입니다. 현재는 기본 모델만 사용할 수 있습니다.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>엔터티 형식

명명 된 엔터티 인식 v3은 여러 형식에서 확장 된 검색을 제공 합니다. 현재 NER v3은 다음과 같은 범주의 엔터티를 인식할 수 있습니다. 지원 되는 엔터티 및 언어의 자세한 목록은 [명명 된 엔터티 형식](../named-entity-types.md) 문서를 참조 하세요.

* 일반
* 개인 정보 

### <a name="request-endpoints"></a>요청 끝점

명명 된 엔터티 인식 v3은 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용 합니다. 요청에 따라 아래 URL 형식을 사용 합니다.

NER
* 일반 엔터티-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 개인 정보 엔터티-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>모델 버전 관리

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>명명 된 엔터티 인식 v2에 대해 지원 되는 형식

> [!NOTE]
> 다음 엔터티는 NER (명명 된 엔터티 인식) 버전 2에서 지원 됩니다. [NER v3](#named-entity-recognition-v3-public-preview) 은 공개 미리 보기 상태 이며 텍스트에서 인식 되는 엔터티의 수와 깊이를 크게 확장 합니다.   

| Type  | 하위 유형 | 예제 |
|:-----------   |:------------- |:---------|
| 사용자        | 해당 없음\*         | "Jeff", "Bill Gates"     |
| 위치      | 해당 없음\*         | “Redmond, Washington”, “Paris”  |
| 조직  | 해당 없음\*         | “Microsoft”   |
| 수량      | 수        | “6”, “six”     |
| 수량      | 백분율    | “50%”, “fifty percent”|
| 수량      | 서수       | “2nd”, “second”     |
| 수량      | 연령           | "90 day old", "30 years old"    |
| 수량      | 통화      | “$10.99”     |
| 수량      | 치수     | “10 miles”, “40 cm”     |
| 수량      | 온도   | “32 degrees”    |
| DateTime      | 해당 없음\*         | “6:30PM February 4, 2012”      |
| DateTime      | 날짜          | “May 2nd, 2017”, “05/02/2017”   |
| DateTime      | 시간          | "8am", "8:00"  |
| DateTime      | DateRange     | “May 2nd to May 5th”    |
| DateTime      | TimeRange     | “6pm to 7pm”     |
| DateTime      | 기간      | “1 minute and 45 seconds”   |
| DateTime      | 설정           | “every Tuesday”     |
| URL           | 해당 없음\*         | "https:\//www.bing.com"    |
| 이메일         | 해당 없음\*         | "support@contoso.com" |
| 미국 전화 번호  | 해당 없음\*         | (미국 전화 번호만 해당) "(312) 555-0176" |
| IP 주소    | 해당 없음\*         | 10.0.0.100 |

\* 입력 및 추출된 엔터티에 따라, 특정 엔터티에 `SubType`이 없을 수 있습니다.  나열 된 모든 지원 되는 엔터티 형식은 영어, 중국어 간체, 프랑스어, 독일어 및 스페인어 언어에 대해서만 사용할 수 있습니다.

### <a name="language-support"></a>언어 지원

여러 언어로 엔터티 링크 설정을 사용하려면 각 언어에서 해당하는 기술 자료를 사용해야 합니다. 텍스트 분석의 엔터티 링크 설정에서 이는 `entities` 엔드포인트에서 지원하는 각 언어가 해당 언어의 Wikipedia 자료에 연결됨을 의미합니다. 자료 크기는 언어마다 다르기 때문에 엔터티 링크 설정 기능의 재현율도 각기 다릅니다. 자세한 내용은 [언어 지원](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) 문서를 참조 하세요.

## <a name="preparation"></a>준비

ID, 텍스트, 언어 형식의 JSON 문서가 있어야 합니다.

현재 지원되는 언어는 [이 목록](../text-analytics-supported-languages.md)을 참조하세요.

문서 크기는 문서당 5,120자 미만이어야 하며, 컬렉션당 최대 1,000개의 항목(ID)을 포함할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다. 다음 예제는 엔터티 링크 설정 끝에 제출할 수 있는 콘텐츠에 대한 설명입니다.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대한 자세한 내용은 [텍스트 분석 API를 호출하는 방법](text-analytics-how-to-call-api.md)에서 찾을 수 있습니다. 편의상 다음 사항을 다시 설명합니다.

+ **POST** 요청을 만듭니다. 이 요청에 대 한 API 설명서를 검토 합니다. [엔터티 api](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용 하 여 키 구 추출에 대 한 HTTP 끝점을 설정 합니다. `/text/analytics/v2.1/entities`를 포함 해야 합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Text Analytics 작업에 대 한 [액세스 키](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 를 포함 하도록 요청 헤더를 설정 합니다.

+ 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나 [설명서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)에서 **API 테스트 콘솔**을 열어 요청을 구성하고, 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

상태 비저장 서비스임에 유의하세요. 계정에는 아무 데이터도 저장되지 않습니다. 결과는 응답으로 즉시 반환됩니다.

## <a name="step-3-view-results"></a>3단계: 결과 보기

모든 POST 요청에서는 ID 및 감지된 속성이 있는 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다.

엔터티 링크 설정의 출력 예는 다음과 같습니다.

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 텍스트 분석을 사용하여 엔터티 링크 설정에 대한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

+ [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)를 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
+ POST 요청은 구독에 유효한 개인 설정 [액세스 키와 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 `/entities` 엔드포인트에 수행되는 요청입니다.
+ 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 애플리케이션에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Text Analytics 개요](../overview.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
* [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712)
