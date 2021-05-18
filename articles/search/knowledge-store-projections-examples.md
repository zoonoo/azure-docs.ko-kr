---
title: 지식 저장소에서 프로젝션 정의
titleSuffix: Azure Cognitive Search
description: Power BI 또는 Azure ML과 함께 사용하기 위해 풍부한 문서를 지식 저장소에 프로젝션하는 방법에 대한 일반적인 패턴의 예입니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97509923"
---
# <a name="how-to-shape-and-export-enrichments"></a>강화 셰이프 및 내보내기 방법

프로젝션은 지식 저장소에 있는 보강 문서의 물리적 식입니다. 보강 문서를 효과적으로 사용하려면 구조가 필요합니다. 이 문서에서는 구조와 관계를 모두 살펴보고, 프로젝션 속성을 구축하는 방법과 생성된 프로젝션 유형 간에 데이터를 연결하는 방법을 알아봅니다. 

프로젝션을 만들기 위해 데이터는 [Shaper 기술](cognitive-search-skill-shaper.md)을 사용하여 사용자 지정 개체를 만들거나 프로젝션 정의 내에서 인라인 셰이핑 구문을 사용하여 모양을 만듭니다. 

데이터 셰이프에는 프로젝션할 모든 데이터가 포함되며 노드 계층 구조로 형성됩니다. 이 문서에서는 보고, 분석 또는 다운스트림 처리에 도움이 되는 물리적 구조로 프로젝션할 수 있도록 데이터를 셰이핑하는 몇 가지 기술을 보여줍니다. 

이 문서에 나온 예제는 이 [REST API 샘플](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)에서 찾을 수 있습니다. 이 샘플은 HTTP 클라이언트에서 다운로드하여 실행할 수 있습니다.

## <a name="introduction-to-projection-examples"></a>프로젝션 예제 소개

다음과 같은 세 가지 유형의 [프로젝션](knowledge-store-projection-overview.md)이 있습니다.

+ 테이블
+ 개체
+ 파일

테이블 프로젝션은 Azure Table 스토리지에 저장됩니다. 개체 및 파일 프로젝션은 Blob Storage에 기록되며, 여기서 개체 프로젝션은 JSON 파일로 저장되고 원본 문서의 콘텐츠는 물론 모든 기술 출력 또는 보강을 포함할 수 있습니다. 또한 보강 파이프라인은 이미지와 같은 이진 파일을 추출할 수 있습니다. 이러한 이진 파일은 파일 프로젝션으로 프로젝션됩니다. 이진 개체를 개체 프로젝션으로 프로젝션하면 연결된 메타데이터만 JSON Blob으로 저장됩니다. 

데이터 셰이핑과 프로젝션의 교차를 이해하기 위해 다양한 구성을 탐색하기 위한 기준으로 다음 기술 세트를 사용합니다. 이 기술 세트는 원시 이미지 및 텍스트 콘텐츠를 처리합니다. 프로젝션은 원하는 시나리오에 대해 문서의 내용과 기술의 출력을 통해 정의됩니다.

> [!IMPORTANT] 
> 프로젝션을 시험해 볼 때 비용 제어를 보장하도록 [인덱서 캐시 속성을 설정](search-howto-incremental-index.md)하는 것이 유용합니다. 인덱서 캐시가 설정되지 않은 경우 프로젝션을 편집하면 전체 문서가 다시 보강됩니다. 캐시가 설정되고 프로젝션만 업데이트되면 이전에 보강된 문서에 대한 기술 세트 실행으로 인해 새로운 Cognitive Services 요금이 발생하지 않습니다.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

null `knowledgeStore`를 기반으로 하는 이 기술 세트를 사용하여 첫 번째 예제에서는 다른 시나리오에서 사용할 수 있는 표 형식 데이터 구조를 만드는 프로젝션으로 구성된 `knowledgeStore` 개체를 채웁니다. 

## <a name="projecting-to-tables"></a>테이블에 프로젝션

Azure Storage의 테이블에 프로젝션하는 것은 Power BI 같은 도구를 사용하여 보고하고 분석하는 데 유용합니다. Power BI는 테이블에서 읽고 프로젝션 중에 생성된 키를 기반으로 관계를 검색할 수 있습니다. 대시보드를 빌드하려는 경우 관련 데이터가 있으면 해당 작업이 간소화됩니다. 

문서에서 추출한 핵심 문구를 단어 클라우드로 시각화하는 대시보드를 빌드해 보겠습니다. 올바른 데이터 구조를 만들려면 기술 세트에 쉐이퍼 기술을 추가하여 문서별 세부 정보 및 핵심 구가 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 `document` 루트 노드에서 `pbiShape`로 호출됩니다.

> [!NOTE] 
> 테이블 프로젝션은 Azure Storage에서 적용하는 스토리지 제한에 따라 관리되는 Azure Storage 테이블입니다. 자세한 내용은 [테이블 스토리지 제한](/rest/api/storageservices/understanding-the-table-service-data-model)을 참조하세요. 엔터티 크기는 1MB를 초과할 수 없으며 단일 속성은 64KB를 초과할 수 없습니다. 이러한 제약 조건으로 인해 테이블은 많은 수의 작은 엔터티를 저장하는 데 좋은 솔루션이 됩니다.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>쉐이퍼 기술을 사용하여 사용자 지정 셰이프 만들기

테이블 스토리지에 프로젝션할 수 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 없으면 프로젝션은 단일 노드(출력당 하나의 프로젝션)만 참조할 수 있습니다. 사용자 지정 셰이프를 만들면 다양한 요소가 단일 테이블로 프로젝션되거나 테이블 컬렉션에 분할 및 분산될 수 있는 새 논리적 전체로 집계됩니다. 

이 예제에서 사용자 지정 도형은 메타데이터와 식별된 엔터티 및 핵심 구를 결합합니다. 개체는 `pbiShape`라고 하며 `/document` 아래에서 부모로 지정됩니다. 

> [!IMPORTANT] 
> 셰이핑의 한 가지 용도는 모든 보강 노드가 잘 구성된 JSON으로 표현되도록 하는 것입니다. 이는 지식 저장소로 프로젝션하는 데 필요합니다. 보강 트리에 잘 구성된 JSON이 아닌 노드가 포함된 경우(예: 보강이 문자열과 같은 기본 요소의 부모로 지정된 경우) 특히 그렇습니다.
>
> 마지막 두 개의 노드인 `KeyPhrases` 및 `Entities`를 확인합니다. 이러한 노드는 `sourceContext`를 사용하여 유효한 JSON 개체에 래핑됩니다. `keyphrases` 및 `entities`는 기본 요소에 대한 보강이며 프로젝션되기 전에 유효한 JSON으로 변환되어야 하므로 필수 항목입니다.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

위의 셰이퍼 기술을 기술 세트에 추가합니다. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

이제 테이블에 프로젝션하는 데 필요한 모든 데이터를 확보했으므로 테이블 정의로 지식 저장소 개체를 업데이트합니다. 이 예제에는 `tableName`, `source` 및 `generatedKeyName` 속성을 설정하여 정의된 세 개의 테이블이 있습니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

다음 단계를 수행하여 작업을 처리할 수 있습니다.

1. ```storageConnectionString``` 속성을 유효한 V2 범용 스토리지 계정 연결 문자열로 설정합니다.  

1. PUT 요청을 실행하여 기술 세트를 업데이트합니다.

1. 기술 세트를 업데이트한 후 인덱서를 실행합니다. 

이제 세 개의 테이블이 있는 작업 프로젝션이 있습니다. 해당 테이블을 Power BI로 가져오면 Power BI가 관계를 자동으로 검색합니다.

다음 예제로 이동하기 전에 테이블 프로젝션의 측면을 다시 살펴보고 데이터 조각화 및 관련 메커니즘을 이해해 보겠습니다.

### <a name="slicing"></a>조각화 

조각화는 전체 통합 셰이프를 구성 부분으로 세분화하는 기술입니다. 결과는 개별적으로 작업할 수 있는 별도의 관련 테이블로 구성됩니다.

예제에서 `pbiShape`는 통합 셰이프(또는 보강 노드)입니다. 프로젝션 정의에서 `pbiShape`는 추가 테이블로 분리되며, 이를 통해 도형(```keyPhrases``` 및 ```Entities```)의 일부를 끌어올 수 있습니다. Power BI에서 이 기능은 여러 엔터티와 keyPhrases가 각 문서에 연결되어 있으므로 유용하며, 엔터티와 keyPhrases를 범주화된 데이터로 볼 수 있으면 더 많은 정보를 얻을 수 있습니다.

조각화는 부모 테이블과 자식 테이블 간의 관계를 암시적으로 생성하며, 부모 테이블의 ```generatedKeyName```을 사용하여 자식 테이블에 동일한 이름의 열을 생성합니다. 

### <a name="naming-relationships"></a>관계 이름 지정

```generatedKeyName```및 ```referenceKeyName``` 속성은 여러 테이블 또는 프로젝션 형식 간에 데이터를 연관하는 데 사용됩니다. 자식 테이블/프로젝션의 각 행에는 부모를 가리키는 속성이 있습니다. 자식의 열 또는 속성 이름은 부모에서 ```referenceKeyName```입니다. ```referenceKeyName```이 제공되지 않은 경우 서비스에서 기본값을 부모의 ```generatedKeyName```으로 지정합니다. 

Power BI는 이러한 생성된 키를 사용하여 테이블 내의 관계를 검색합니다. 자식 테이블의 열이 다른 이름으로 지정 되어야 하는 경우 부모 테이블에서 ```referenceKeyName``` 속성을 설정합니다. 한 가지 예를 들면 ```generatedKeyName```을 pbiDocument 테이블의 ID로 설정하고 ```referenceKeyName```을 DocumentID로 설정하는 것입니다. 그러면 문서 ID가 포함된 pbiEntities 및 pbiKeyPhrases 테이블의 열이 DocumentID로 명명됩니다.

## <a name="projecting-to-objects"></a>개체에 프로젝션

개체 프로젝션은 테이블 프로젝션과 동일한 제한이 없으며 큰 문서를 프로젝션하는 데 더 적합합니다. 이 예제에서 전체 문서는 개체 프로젝션으로 전송됩니다. 개체 프로젝션은 컨테이너의 단일 프로젝션으로 제한되며 조각화할 수 없습니다.

개체 프로젝션을 정의하려면 프로젝션에서 ```objects``` 배열을 사용합니다. 쉐이퍼 기술을 사용하여 새 도형을 생성하거나 개체 프로젝션의 인라인 셰이핑을 사용할 수 있습니다. 테이블 예제에서는 도형을 만들고 자르는 방법을 보여주지만, 이 예제에서는 인라인 셰이핑을 사용하는 방법을 보여줍니다. 

인라인 셰이핑은 프로젝션에 대한 입력 정의에서 새 도형을 만드는 기능입니다. 인라인 셰이핑은 쉐이퍼 기술이 생성하는 것과 동일한 익명 개체를 만듭니다(이 경우 `pbiShape`). 인라인 셰이핑은 다시 사용할 계획이 없는 도형을 정의하는 경우에 유용합니다.

프로젝션 속성은 배열입니다. 이 예제에서는 knowledgeStore 정의에 인라인 프로젝션이 포함된 새 프로젝션 인스턴스를 배열에 추가합니다. 인라인 프로젝션을 사용하는 경우 셰이퍼 기술을 생략할 수 있습니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>파일에 프로젝션

파일 프로젝션은 원본 문서에서 추출된 이미지이거나 보강 프로세스에서 프로젝션될 수 있는 보강의 출력입니다. 개체 프로젝션과 비슷한 파일 프로젝션은 Azure Storage에서 Blob으로 구현되고 이미지를 포함합니다. 

파일 프로젝션을 생성하려면 프로젝션 개체에서 `files` 배열을 사용합니다. 이 예제에서는 문서에서 추출한 모든 이미지를 `samplefile`라는 컨테이너로 프로젝션합니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>여러 형식으로 프로젝션

복잡한 시나리오에서는 프로젝션 형식 간에 콘텐츠를 프로젝션해야 할 수 있습니다. 예를 들어 핵심 문구 및 엔터티와 같은 일부 데이터를 테이블에 프로젝션해야 하는 경우 텍스트 및 레이아웃 텍스트의 OCR 결과를 개체로 저장한 후에 이미지를 파일로 프로젝션합니다. 

이 예제에서는 기술 세트를 다음과 같은 변경 내용으로 업데이트합니다.

1. 각 문서에 행이 있는 테이블을 만듭니다.
1. 이 테이블에서 행으로 식별되는 각 키 구문을 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 이 테이블에서 행으로 식별되는 각 엔터티를 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 각 이미지에 대한 레이아웃 텍스트를 사용하여 개체 프로젝션을 만듭니다.
1. 추출된 각 이미지를 프로젝션하는 파일 프로젝션을 만듭니다.
1. 문서 테이블에 대한 참조, 레이아웃 텍스트가 있는 개체 프로젝션 및 파일 프로젝션이 포함된 상호 참조 테이블을 만듭니다.

이러한 변경 내용은 지식 저장소 정의에 추가로 반영됩니다. 

### <a name="shape-data-for-cross-projection"></a>교차 프로젝션을 위한 셰이프 데이터

이러한 프로젝션에 필요한 셰이프를 가져오려면 먼저 `crossProjection`이라는 모양의 개체를 만드는 새로운 셰이퍼 기술을 추가합니다. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 정의

통합된 crossProjection 개체에서 개체를 여러 테이블로 분할하고 OCR 출력을 Blob으로 캡처한 다음, 이미지를 파일(Blob Storage에도 있음)로 저장합니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

개체 프로젝션에는 각 프로젝션에 대한 컨테이너 이름이 필요하며, 개체 프로젝션 또는 파일 프로젝션은 컨테이너를 공유할 수 없습니다. 

### <a name="relationships-among-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 간의 관계

또한 이 예제에서는 프로젝션의 또 다른 기능을 강조합니다. 동일한 프로젝션 개체 내에서 여러 유형의 프로젝션을 정의하면 서로 다른 형식(테이블, 개체, 파일) 내부 및 전체에서 표현되는 관계가 있습니다. 이를 통해 문서의 테이블 행으로 시작하고 개체 프로젝션에서 해당 문서 내의 이미지에 대한 모든 OCR 텍스트를 찾을 수 있습니다. 

관련 데이터를 원하지 않는 경우 다른 프로젝션 개체에서 프로젝션을 정의합니다. 예를 들어 다음 코드 조각은 테이블과 개체(OCR 텍스트) 프로젝션 간의 관계를 제외하고 테이블의 관련 관계를 생성합니다. 

프로젝션 그룹은 서로 다른 요구에 대해 동일한 데이터를 서로 다른 모양으로 프로젝션하려는 경우에 유용합니다. 예를 들어 Power BI 대시보드에 대한 프로젝션 그룹과 사용자 지정 기술에 래핑된 기계 학습 모델을 교육하는 데 사용되는 데이터를 캡처하기 위한 또 다른 프로젝션 그룹이 있습니다.

여러 유형의 프로젝션을 빌드할 때 파일 및 개체 프로젝션이 먼저 생성되고 경로가 테이블에 추가됩니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>일반적인 문제

프로젝션을 정의할 때 예상치 못한 결과를 초래할 수 있는 몇 가지 일반적인 문제가 있습니다. 지식 저장소의 출력이 예상과 다른 경우 이러한 문제를 확인합니다.

+ 문자열 보강을 유효한 JSON으로 셰이핑하지 않습니다. 예를 들어 키 구로 보강된 `merged_content` 문자열이 보강되면 보강된 속성은 보강 트리 내에서 `merged_content`의 자식으로 표시됩니다. 기본 표현은 잘 구성된 JSON이 아닙니다. 따라서 프로젝션 시 이름과 값이 있는 유효한 JSON 개체로 보강을 변환해야 합니다.

+ 원본 경로의 끝에 ```/*```를 생략합니다. 프로젝션의 원본이 `/document/pbiShape/keyPhrases`인 경우 키 구 배열은 단일 개체/행으로 프로젝션됩니다. 대신의 원본 경로를 `/document/pbiShape/keyPhrases/*`로 설정하여 각 키 구에 대해 단일 행 또는 개체를 생성합니다.

+ 경로 구문 오류입니다. 경로 선택기는 대/소문자를 구분 하며 선택기에 대해 정확한 대/소문자를 사용하지 않는 경우 입력 경고가 누락될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 예제에서는 프로젝션을 만드는 방법에 대한 일반적인 패턴을 보여줍니다. 개념을 이해했으므로 이제 특정 시나리오에 대한 프로젝션을 빌드할 준비가 되었습니다.

새 기능을 탐색할 때 다음 단계로 증분 보강을 고려합니다. 증분 보강은 캐싱을 기반으로 하므로 모든 보강을 다시 사용할 수 있으며 기술 세트 수정의 영향을 받지 않습니다. 이는 OCR 및 이미지 분석이 포함된 파이프라인에 특히 유용합니다.

> [!div class="nextstepaction"]
> [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)

프로젝션에 대한 개요의 경우 그룹 및 조각화와 같은 기능 및 [기술 세트에서 정의](knowledge-store-projection-overview.md)하는 방법을 자세히 알아보세요.

> [!div class="nextstepaction"]
> [지식 저장소의 프로젝션](knowledge-store-projection-overview.md)