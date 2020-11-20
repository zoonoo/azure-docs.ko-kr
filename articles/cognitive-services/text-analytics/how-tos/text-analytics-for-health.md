---
title: 상태에 Text Analytics를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: 상태에 대 한 Text Analytics를 사용 하 여 비구조적 임상 텍스트에서 의료 정보를 추출 하 고 레이블 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: e7f017c1f3dc189af2b0fc053912decca3459478
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952763"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>방법: 상태에 대 한 Text Analytics 사용 (미리 보기)

> [!IMPORTANT] 
> 상태 Text Analytics은 "있는 그대로" 및 "모든 오류를 포함 하 여" 제공 되는 미리 보기 기능입니다. 따라서 **상태 (미리 보기)에 대 한 Text Analytics는 프로덕션 환경에서 구현 하거나 배포 해서는** 안 됩니다. 상태에 대 한 Text Analytics는 질병 또는 기타 조건에 대 한 진단, 치료, 완화, 처리 또는 방지에 사용 하기 위한 의료 장치, 임상 지원, 진단 도구 또는 기타 기술로 사용할 수 없으며, Microsoft가 이러한 목적을 위해이 기능을 사용할 수 있는 라이선스 또는 권한이 부여 되지 않습니다. 이 기능은 의료 전문가의 전문 의료 통지, 의료 보험 통지, 진단, 처리 또는 임상를 위한 대체 방법으로 설계 되거나 구현 하거나 배포 하기 위한 것이 아니며, 사용 하지 않아야 합니다. 고객은 상태에 대 한 Text Analytics의 사용만을 담당 합니다. Microsoft는 의료 목적을 위해이 기능을 사용 하 여 제공 되는 Text Analytics 또는 모든 사람의 의료 요구 사항을 충족 하는 경우에만이 기능을 제공 합니다. 


상태에 대 한 Text Analytics는 의료 노트, 방전 요약, 임상 문서 및 전자 상태 레코드와 같은 구조화 되지 않은 텍스트에서 관련 의료 정보를 추출 하 고 레이블 하는 텍스트 분석 API 서비스의 기능입니다.  이 서비스를 활용 하는 방법에는 두 가지가 있습니다. 

* 웹 기반 API (비동기) 
* Docker 컨테이너 (동기)   

## <a name="features"></a>기능

상태 Text Analytics는 NER (명명 된 엔터티 인식), 관계 추출, 엔터티 부정 및 영어 텍스트에 대 한 엔터티 링크를 수행 하 여 비구조적 임상 및 생명 공학 텍스트에서 정보를 파악 합니다.

### <a name="named-entity-recognition"></a>[명명된 엔터티 인식](#tab/ner)

명명 된 엔터티 인식은 진단, 조제 이름, 증상/기호 또는 연령와 같은 하나 이상의 의미 체계 형식과 연결 될 수 있는 구조화 되지 않은 텍스트에서 언급 된 단어와 구를 검색 합니다.

> [!div class="mx-imgBorder"]
> ![상태 NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[관계 추출](#tab/relation-extraction)

관계 추출은 텍스트에 언급 된 개념 간의 의미 있는 연결을 식별 합니다. 예를 들어 "조건 시간" 관계는 조건 이름을 시간에 연결 하 여 찾을 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![상태 재지정](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[엔터티 연결](#tab/entity-linking)

엔터티 연결은 텍스트에 언급 된 명명 된 엔터티를 미리 정의 된 개념의 데이터베이스에 있는 개념에 연결 하 여 고유 엔터티를 구분 합니다. 예를 들어, 통합 의료 언어 시스템 (UMLS)이 있습니다.

> [!div class="mx-imgBorder"]
> ![상태 EL](../media/ta-for-health/health-entity-linking.png)

상태에 대 한 Text Analytics는[Umls](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(통합 의료 언어 시스템) Metathesaurus 기술 자료에 있는 상태 및 생명 공학 어휘에 연결 하는 것을 지원 합니다.

### <a name="negation-detection"></a>[부정 감지](#tab/negation-detection) 

의료 콘텐츠의 의미는 부정과 같은 한정자의 영향을 받습니다 .이는 잘못 진단 된 경우 중요 한 함축을 가질 수 있습니다. 상태에 대 한 Text Analytics는 텍스트에 언급 된 여러 엔터티의 부정 검색을 지원 합니다. 

> [!div class="mx-imgBorder"]
> ![상태 NEG](../media/ta-for-health/health-negation.png)

---

지원 되는 엔터티의 전체 목록에 대해서는 Text Analytics에서 반환 된 [엔터티 범주](../named-entity-types.md?tabs=health) 를 참조 하세요.

### <a name="supported-languages-and-regions"></a>지원 되는 언어 및 지역

상태 Text Analytics는 영어 문서만 지원 합니다. 

상태 호스트 된 web API에 대 한 Text Analytics은 현재 미국 서 부 2, 미국 동부 2, 미국 중부, 유럽, 유럽 및 유럽 서부 지역 에서만 사용할 수 있습니다.

## <a name="request-access-to-the-public-preview"></a>공개 미리 보기에 대 한 액세스 요청

상태 공개 미리 보기에 대 한 Text Analytics에 대 한 액세스를 요청 하는 [Cognitive Services 요청 양식을](https://aka.ms/csgate) 작성 하 고 제출 합니다. 상태 사용에 대 한 Text Analytics 요금은 청구 되지 않습니다. 

이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식이 제출 되 면 Azure Cognitive Services 팀에서 검토 하 고 의사 결정을 내립니다.

> [!IMPORTANT]
> * 양식에서 Azure 구독 ID와 연결 된 전자 메일 주소를 사용 해야 합니다.
> * 사용 하는 Azure 리소스는 승인 된 Azure 구독 ID로 생성 되어야 합니다. 
> * Microsoft에서 응용 프로그램의 상태에 대 한 업데이트를 확인 하려면 전자 메일 (받은 편지함 및 정크 폴더 모두)을 확인 합니다.

## <a name="using-the-docker-container"></a>Docker 컨테이너 사용 

사용자 환경에서 health 컨테이너에 Text Analytics를 실행 하려면 다음 지침에 따라 [컨테이너를 다운로드](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)하 여 설치 합니다.

## <a name="using-the-client-library"></a>클라이언트 라이브러리 사용

Text Analytics 클라이언트 라이브러리의 최신 시험판을 사용 하면 클라이언트 개체를 사용 하 여 상태에 대 한 Text Analytics를 호출할 수 있습니다. 참조 설명서를 참조 하 고 GitHub의 예제를 참조 하세요.
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기 

### <a name="preparation"></a>준비

상태에 대 한 Text Analytics 작업에 더 적은 양의 텍스트를 제공할 때 품질 높은 결과를 생성 합니다. 이는 더 큰 텍스트 블록에서 더 나은 성능을 제공 하는 키 구 추출을 비롯 한 다른 Text Analytics 기능 중 일부와 반대입니다. 이러한 작업에서 최상의 결과를 얻으려면 입력을 적절 하 게 재구성 하는 것이 좋습니다.

다음 형식의 JSON 문서가 있어야 합니다. ID, 텍스트 및 언어. 

문서 크기는 문서당 5,120자 미만이어야 합니다. 컬렉션에 허용 되는 최대 문서 수는 개념의 [데이터 제한](../concepts/data-limits.md?tabs=version-3) 문서를 참조 하세요. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>호스트 된 비동기 웹 API에 대 한 API 요청 구성

컨테이너와 호스트 된 web API 모두에 대해 POST 요청을 만들어야 합니다. [상태 호스팅 api 참조에 대 한 Text Analytics](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) 에서 [Postman](text-analytics-how-to-call-api.md), 말아 넘기기 명령 또는 **api 테스트 콘솔** 을 사용 하 여 원하는 지역의 호스트 된 web api에 대 한 POST 요청을 빠르게 생성 하 고 보낼 수 있습니다. 

다음은 상태 API 요청의 게시 본문에 대 한 Text Analytics에 연결 된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>호스트 된 비동기 웹 API 응답 

이 POST 요청은 비동기 작업에 대 한 작업을 제출 하는 데 사용 되므로 응답 개체에 텍스트가 없습니다.  그러나 작업 및 출력의 상태를 확인 하는 GET 요청을 수행 하려면 응답 헤더에 작업-위치 키 값이 있어야 합니다.  다음은 POST 요청의 응답 헤더에 있는 작업 위치 키 값의 예입니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

작업 상태를 확인 하려면 POST 응답의 작업-위치 키 헤더 값에 있는 URL에 대 한 GET 요청을 수행 합니다.  작업 상태를 반영 하는 데 사용 되는 상태는,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` 및 `cancelled` 입니다.  

`NotStarted` `running` GET 요청과 동일한 URL에 대 한 DELETE HTTP 호출을 포함 하는 또는 상태의 작업을 취소할 수 있습니다.  DELETE 호출에 대 한 자세한 내용은 [상태 호스팅 API 참조에 대 한 Text Analytics를 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)하세요.

다음은 GET 요청에 대 한 응답의 예입니다.  출력을 제거 하 고 나 `expirationDateTime` 서 (작업이 만들어진 시간부터 24 시간)이 경과 될 때까지 출력을 검색할 수 있습니다.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>컨테이너에 대 한 API 요청 구조

[Postman](text-analytics-how-to-call-api.md) 또는 아래의 예제 말아 request를 사용 하 여 배포 된 컨테이너에 쿼리를 제출 하 고 `serverURL` 변수를 적절 한 값으로 바꿀 수 있습니다.  컨테이너의 URL에 있는 API 버전은 호스트 된 API와 다릅니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

다음 JSON은 상태 API 요청의 POST 본문에 대 한 Text Analytics에 연결 된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>컨테이너 응답 본문

다음 JSON은 컨테이너 화 된 동기 호출의 상태 API 응답 본문에 대 한 Text Analytics의 예입니다.

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>부정 감지 출력

부정 감지를 사용 하는 경우, 경우에 따라 단일 부정 단어가 한 번에 여러 용어를 해결할 수 있습니다. 인식 되는 엔터티의 부정은 플래그의 부울 값으로 JSON 출력에 표시 됩니다 `isNegated` . 예를 들면 다음과 같습니다.

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>관계 추출 출력

관계 추출 출력에는 관계의 *원본* 및 *대상* 에 대 한 URI 참조가 포함 됩니다. 관계 역할이 인 엔터티 `ENTITY` 는 필드에 할당 됩니다 `target` . 관계 역할이 인 엔터티 `ATTRIBUTE` 는 필드에 할당 됩니다 `source` . 약어 관계는 양방향 `source` 및 `target` 필드를 포함 하며 `bidirectional` 로 설정 됩니다 `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [명명 된 엔터티 범주](../named-entity-types.md)
* [새로운 기능](../whats-new.md)
