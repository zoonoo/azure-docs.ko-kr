---
title: 명함 - 양식 인식기
titleSuffix: Azure Cognitive Services
description: Form Recognizer API - 사용 및 제한으로 명함 분석과 관련된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5211c1263af599eb5fd09ad276545c725ce5c867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466997"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>양식 인식기로 미리 작성된 명함 모델 

Azure Form Recognizer는 미리 작성된 명함 모델을 사용하여 명함에서 연락처 정보를 분석하고 추출할 수 있습니다. 강력한 OCR(광학 문자 인식) 기능을 명함 이해 모델과 결합하여 영어로 된 명함의 주요 정보를 추출합니다. 개인 연락처 정보, 회사 이름, 직위 등을 추출합니다. 미리 작성된 비즈니스 카드 API는 양식 인식기 v2.1 미리 보기에서 공개적으로 사용할 수 있습니다. 

## <a name="what-does-the-business-card-service-do"></a>비즈니스 카드 서비스란 무엇인가요?

미리 작성된 비즈니스 카드 API는 명함에서 키 필드를 추출하여 구성된 JSON 응답으로 반환합니다.

![FOTT + JSON으로 출력된 Contoso의 항목별 이미지](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>추출된 필드

|이름| Type | Description | 텍스트 | 
|:-----|:----|:----|:----|
| ContactNames | 개체의 배열 | 명함에서 추출 된 연락처 이름 | [{ "FirstName": "John", "LastName": "Doe" }] |
| FirstName | 문자열 | 연락처의 이름 | "John" | 
| LastName | 문자열 | 연락처의 성 |     "Doe" | 
| CompanyNames | 문자열 배열 | 명함에서 추출된 회사 이름 | ["Contoso"] | 
| Departments | 문자열 배열 | 연락처의 부서 또는 조직 | ["R&D"] | 
| JobTitles | 문자열 배열 | 연락처의 나열된 직함 | ["Software Engineer"] | 
| 전자 메일 | 문자열 배열 | 명함에서 추출된 연락처 이메일 | ["johndoe@contoso.com"] | 
| Websites | 문자열 배열 | 명함에서 추출된 웹사이트 | ["https://www.contoso.com"] | 
| 주소 | 문자열 배열 | 명함에서 추출된 주소 | ["123 Main Street, Redmond, WA 98052"] | 
| MobilePhones | 전화 번호 배열 | 명함에서 추출된 휴대폰 번호 | ["+19876543210"] |
| Faxes | 전화 번호 배열 | 명함에서 추출된 팩스 번호 | ["+19876543211"] |
| WorkPhones | 전화 번호 배열 | 명함에서 추출된 회사 전화 번호 | ["+19876543231"] |
| OtherPhones     | 전화 번호 배열 | 명함에서 추출된 기타 전화 번호 | ["+19876543233"] |


비즈니스 카드 API는 명함에서 인식된 모든 텍스트를 반환할 수도 있습니다. 이 OCR 출력은 JSON 응답에 포함됩니다.  

### <a name="input-requirements"></a>입력 요구 사항 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>명함 분석 작업

[명함 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)은 명함의 이미지 또는 PDF를 입력으로 사용하여 관심 있는 값을 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>명함 분석 결과 가져오기 작업

두 번째 단계는 [명함 분석 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult) 작업을 호출하는 것입니다. 이 작업은 명함 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다. **succeeded** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작되지 않았습니다.<br /><br />running: 분석 작업이 진행 중입니다.<br /><br />failed: 분석 작업이 실패했습니다.<br /><br />succeeded: 분석 작업이 성공했습니다.|

**상태** 필드의 값이 **succeeded** 이면 JSON 응답에는 요청된 경우 명함 해석 및 선택적 텍스트 인식 결과가 포함됩니다. 명함 해석 결과는 명명된 필드 값의 사전으로 구성됩니다. 여기서 각 값에는 추출된 텍스트, 정규화된 값, 경계 상자, 신뢰도 및 해당 단어 요소가 포함됩니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보와 함께 줄과 단어의 계층 구조로 구성됩니다.

![명함 출력 샘플](./media/business-card-results.png)

### <a name="sample-json-output"></a>JSON 출력 샘플

명함 분석 결과 가져오기 작업에 대한 응답은 모든 정보가 추출된 명함을 구조화된 방식으로 표현한 것입니다.  [명함 파일 샘플](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) 및 구조화된 출력 [명함 출력 샘플](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)은 여기를 참조하세요.

성공적인 JSON 응답의 다음 예제를 참조하세요.
* `"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. 
* `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기서 이름, 성, 회사 이름 등과 같은 유용한 연락처 정보를 볼 수 있습니다.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

[빠른 시작](./QuickStarts/client-library.md)에 따라 Python 및 REST API 사용하여 명함 데이터 추출을 구현합니다.

## <a name="customer-scenarios"></a>고객 시나리오  

비즈니스 카드 API를 사용하여 추출된 데이터를 사용하여 다양한 작업을 수행할 수 있습니다. 이 연락처 정보를 추출하면 클라이언트 관련 역할을 하는 사용자의 시간이 자동으로 절감됩니다. 다음은 고객이 비즈니스 카드 API를 통해 달성한 몇 가지 예입니다.

* 명함에서 연락처 정보를 추출하고 신속하게 전화 연락처를 만듭니다. 
* CRM과 통합하여 명함 이미지를 사용해 연락처를 자동으로 만듭니다. 
* 판매 잠재 고객을 계속 추적합니다.  
* 기존 명함 이미지에서 연락처 정보를 대량으로 추출합니다. 

또한 비즈니스 카드 API는 [AI 빌더 명함 처리 기능](/ai-builder/prebuilt-business-card)을 강화합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작](./quickstarts/client-library.md)에 따라 명함 인식을 시작합니다.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
