---
title: 지식 저장소에서 투영 정의
titleSuffix: Azure Cognitive Search
description: Power BI 또는 Azure ML과 함께 사용할 수 있도록 풍부한 문서를 기술 저장소에 프로젝트하는 방법에 대한 일반적인 패턴의 예입니다.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943672"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>지식 저장소 예측: 보강을 형성하고 내보내는 방법

> [!IMPORTANT] 
> 지식 저장소는 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-Preview](search-api-preview.md)는 미리 보기 기능을 제공합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

프로젝션은 지식 저장소에서 보강된 문서의 물리적 표현입니다. 보강 된 문서를 효과적으로 사용하려면 구조가 필요합니다. 이 문서에서는 구조와 관계를 모두 살펴보고 프로젝션 속성을 빌드하는 방법과 만드는 프로젝션 유형 간에 데이터를 관련하는 방법을 알아보십시오. 

프로젝션을 만들려면 [Shaper 기술을](cognitive-search-skill-shaper.md) 사용하여 데이터를 셰이핑하여 사용자 지정 개체를 만들거나 투영 정의 내에서 인라인 형성 구문을 사용해야 합니다. 

데이터 셰이프에는 노드계층으로 구성된 투영하려는 모든 데이터가 포함됩니다. 이 문서에서는 보고, 분석 또는 다운스트림 처리에 도움이 되는 물리적 구조로 프로젝트할 수 있도록 데이터를 셰이핑하는 몇 가지 기술을 보여 줍니다. 

이 문서에 제시된 예제는 HTTP 클라이언트에서 다운로드하여 실행할 수 있는 REST [API 샘플에서](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)찾을 수 있습니다.

## <a name="introduction-to-the-examples"></a>예제 소개

[프로젝션에](knowledge-store-projection-overview.md)익숙한 경우 세 가지 유형이 있음을 기억합니다.

+ 테이블
+ 개체
+ 파일

테이블 프로젝션은 Azure 테이블 저장소에 저장됩니다. 개체 및 파일 프로젝션은 개체 프로젝션이 JSON 파일로 저장되고 소스 문서의 콘텐츠와 기술 출력 또는 보강을 포함할 수 있는 Blob 저장소에 기록됩니다. 보강 파이프라인은 이미지와 같은 바이너리를 추출할 수도 있으며, 이러한 바이너리는 파일 프로젝션으로 투영됩니다. 이진 개체가 개체 프로젝션으로 프로젝션되면 이와 관련된 메타데이터만 JSON Blob으로 저장됩니다. 

데이터 셰이핑과 프로젝션 간의 교차를 이해하기 위해 다음 기술 집합을 다양한 구성탐색의 기초로 사용합니다. 이 기술 집합은 원시 이미지 및 텍스트 콘텐츠를 처리합니다. 지원하려는 시나리오에 대해 문서의 내용과 기술 출력에서 프로젝션이 정의됩니다.

> [!IMPORTANT] 
> 예측을 실험할 때 비용 제어를 위해 [인덱서 캐시 속성을 설정하는](search-howto-incremental-index.md) 것이 유용합니다. 프로젝션을 편집하면 인덱서 캐시가 설정되지 않으면 전체 문서가 다시 보강됩니다. 캐시가 설정되고 프로젝션만 업데이트되면 이전에 보강된 문서에 대한 기술 집합 실행으로 인해 새로운 코그너티브 서비스 요금이 발생하지 않습니다.

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

null을 기준으로 이 기술 `knowledgeStore` 집합을 사용하여 첫 번째 예제는 다른 시나리오에서 사용할 수 있는 테이블 형식 데이터 구조를 만드는 프로젝션으로 구성된 `knowledgeStore` 개체를 채웁니다. 

## <a name="projecting-to-tables"></a>테이블에 투영

Azure 저장소의 테이블에 투영하는 것은 Power BI와 같은 도구를 사용하여 보고 및 분석에 유용합니다. Power BI는 테이블에서 읽고 프로젝션 중에 생성된 키를 기반으로 관계를 검색할 수 있습니다. 대시보드를 빌드하려는 경우 관련 데이터를 사용하면 해당 작업이 간소화됩니다. 

문서에서 추출한 핵심 구를 워드 클라우드로 시각화할 수 있는 대시보드를 만들려고 한다고 가정해 보겠습니다. 올바른 데이터 구조를 만들려면 기술 집합에 Shaper 기술을 추가하여 문서별 세부 정보와 핵심 구가 있는 사용자 지정 셰이프를 만들 수 있습니다. 사용자 지정 셰이프는 루트 노드에서 호출됩니다. `pbiShape` `document`

> [!NOTE] 
> 테이블 프로젝션은 Azure Storage 테이블로, Azure Storage에서 부과하는 저장소 제한에 의해 제어됩니다. 자세한 내용은 [테이블 저장소 제한을](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)참조하십시오. 엔터티 크기가 1MB를 초과할 수 없고 단일 속성이 64KB를 초과할 수 없다는 것을 아는 것이 유용합니다. 이러한 제약 조건은 테이블을 많은 수의 작은 엔터티를 저장하기에 좋은 솔루션입니다.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>셰이퍼 기술을 사용하여 사용자 지정 셰이프 만들기

테이블 저장소에 투영할 수 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 없으면 프로젝션은 단일 노드(출력당 하나의 프로젝션)만 참조할 수 있습니다. 사용자 지정 셰이프를 만들면 다양한 요소를 단일 테이블로 투영하거나 테이블 컬렉션에 분산하여 분할하고 배포할 수 있는 새로운 논리적 전체로 집계할 수 있습니다. 

이 예제에서 사용자 지정 셰이프는 메타데이터와 식별된 엔터티 및 핵심 구를 결합합니다. 개체가 호출되고 `pbiShape` 에서 `/document`부모로 표시됩니다. 

> [!IMPORTANT] 
> 셰이핑의 한 가지 목적은 모든 보강 노드가 지식 저장소에 투영하는 데 필요한 잘 형성된 JSON으로 표현되도록 하는 것입니다. 보강 트리에 잘 형성되지 않은 노드가 포함되어 있는 경우(예: 보강이 문자열과 같은 기본에 상위화되는 경우) 특히 그렇습니다.
>
> 마지막 두 `KeyPhrases` 노드와 `Entities`. 이 개체는 `sourceContext`에서 유효한 JSON 개체로 래핑됩니다. 이는 프리미티브에 대한 보강이 `keyphrases` 필요하고 `entities` 투영되기 전에 유효한 JSON으로 변환해야 합니다.
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

위의 셰이퍼 스킬을 스킬세트에 추가합니다. 

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

이제 테이블에 투영하는 데 필요한 모든 데이터가 있으므로 table 정의로 knowledgeStore 개체를 업데이트합니다. 이 예제에서는 에서 및 `tableName` `source` `generatedKeyName` 속성을 설정 하 여 정의 된 세 개의 테이블이 있습니다.

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

다음 단계에 따라 작업을 처리할 수 있습니다.

1. ```storageConnectionString``` 속성을 유효한 V2 범용 저장소 계정 연결 문자열로 설정합니다.  

1. PUT 요청을 실행하여 기술 집합을 업데이트합니다.

1. 기술 집합을 업데이트한 후 인덱서를 실행합니다. 

이제 세 개의 테이블이 있는 작업 프로젝션이 있습니다. 이러한 테이블을 Power BI로 가져오면 Power BI가 관계를 자동으로 검색해야 합니다.

다음 예제로 이동하기 전에 테이블 프로젝션의 측면을 다시 방문하여 데이터를 잘라내고 관련하는 메커니즘을 이해할 수 있습니다.

### <a name="slicing"></a>깔 끔 히 

슬라이스는 전체 통합 된 모양을 구성 부분으로 세분화하는 기술입니다. 결과는 개별적으로 작업할 수 있는 별도의 관련 테이블로 구성됩니다.

이 예에서는 `pbiShape` 통합 셰이프(또는 보강 노드)가 있습니다. 투영 정의에서 `pbiShape` 추가 테이블로 슬라이스되어 셰이프의 일부를 빼낼 수 ```keyPhrases``` ```Entities```있습니다. Power BI에서는 여러 엔터티와 keyPhrase가 각 문서와 연결되기 때문에 유용하며 엔터티 및 keyPhrase를 분류된 데이터로 볼 수 있으면 더 많은 통찰력을 얻을 수 있습니다.

슬라이싱은 부모 테이블과 자식 테이블 간의 관계를 ```generatedKeyName``` 암시적으로 생성하며, 부모 테이블의 경우 자식 테이블에서 이름이 같은 열을 만듭니다. 

### <a name="naming-relationships"></a>관계 이름 지정

```generatedKeyName``` 및 ```referenceKeyName``` 속성은 테이블 간에 또는 프로젝션 형식 간에 데이터를 관련하는 데 사용됩니다. 하위 테이블/프로젝션의 각 행에는 부모를 다시 가리키는 속성이 있습니다. 하위 의 열 또는 속성의 이름은 ```referenceKeyName``` 부모로부터의 이름입니다. 를 ```referenceKeyName``` 제공하지 않는 경우 서비스는 ```generatedKeyName``` 부모로부터 기본값으로 설정합니다. 

Power BI는 이러한 생성된 키를 사용하여 테이블 내의 관계를 검색합니다. 하위 테이블의 이름이 다르게 지정된 열이 필요한 ```referenceKeyName``` 경우 상위 테이블에 속성을 설정합니다. 한 가지 예는 ```generatedKeyName``` pbiDocument 테이블의 ID와 ```referenceKeyName``` DocumentID로 설정하는 것입니다. 이렇게 하면 pbiEntities 및 pbiKeyPhrases 테이블의 열에 문서 ID라는 이름이 들어 있습니다.

## <a name="projecting-to-objects"></a>객체에 투영

개체 투영은 테이블 프로젝션과 동일한 제한이 없으며 큰 문서를 투영하는 데 더 적합합니다. 이 예제에서는 전체 문서를 개체 투영에 투영합니다. 개체 프로젝션은 컨테이너의 단일 프로젝션으로 제한되며 슬라이스할 수 없습니다.

개체 프로젝션을 정의하려면 ```objects``` 프로젝션에서 배열을 사용합니다. 셰이퍼 기술을 사용하여 새 셰이프를 생성하거나 오브젝트 프로젝션의 인라인 셰이핑을 사용할 수 있습니다. 표 예제에서는 셰이프 를 만들고 자르는 방법을 보여 주었지만 이 예제에서는 인라인 셰이핑의 사용을 보여 줍니다. 

인라인 셰이핑은 투영에 대한 입력의 정의에서 새 셰이프를 만드는 기능입니다. 인라인 셰이핑은 셰이퍼 기술이 생성하는 것과 동일한 익명 객체를 만듭니다(이 `pbiShape`경우). 인라인 셰이핑은 재사용하지 않을 셰이프를 정의하는 경우에 유용합니다.

프로젝션 속성은 배열입니다. 이 예제에서는 knowledgeStore 정의에 인라인 프로젝션이 포함된 배열에 새 프로젝션 인스턴스를 추가합니다. 인라인 프로젝션을 사용하는 경우 셰이퍼 기술을 생략할 수 있습니다.

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

## <a name="projecting-to-file"></a>파일에 투영

파일 프로젝션은 소스 문서에서 추출된 이미지또는 보강 프로세스에서 프로젝션할 수 있는 보강 출력입니다. 개체 프로젝션과 유사한 파일 프로젝션은 Azure Storage의 Blob으로 구현되고 이미지를 포함합니다. 

파일 프로젝션을 생성하려면 `files` 프로젝션 오브젝트의 배열을 사용합니다. 이 예제는 문서에서 추출한 모든 이미지를 `samplefile`.라는 컨테이너에 투영합니다.

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

## <a name="projecting-to-multiple-types"></a>여러 유형으로 투영

더 복잡한 시나리오에서는 프로젝션 유형 간에 콘텐츠를 투영해야 할 수 있습니다. 예를 들어 키 구 및 엔터티와 같은 일부 데이터를 테이블에 투영해야 하는 경우 텍스트 및 레이아웃 텍스트의 OCR 결과를 개체로 저장한 다음 이미지를 파일로 프로젝슬합니다. 

이 예제에서는 기술 집합에 대한 업데이트에는 다음 변경 사항이 포함됩니다.

1. 각 문서에 대한 행이 있는 테이블을 만듭니다.
1. 이 테이블의 행으로 식별된 각 키 구를 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 이 테이블의 행으로 식별된 각 엔터티를 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 각 이미지의 레이아웃 텍스트로 개체 투영을 만듭니다.
1. 추출된 각 이미지를 투영하여 파일 프로젝션을 만듭니다.
1. 문서 테이블에 대한 참조, 레이아웃 텍스트가 있는 개체 투영 및 파일 프로젝션이 포함된 교차 참조 테이블을 만듭니다.

이러한 변경 내용은 지식 스토어 정의에 더 아래로 반영됩니다. 

### <a name="shape-data-for-cross-projection"></a>교차 프로젝션을 위한 셰이프 데이터

이러한 프로젝션에 필요한 셰이프를 얻으려면 먼저 셰이퍼 기술을 추가하여 모양이 있는 오브젝트를 `crossProjection`만듭니다. 

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

통합된 crossProjection 개체에서 개체를 여러 테이블로 분할하고 OCR 출력을 Blob으로 캡처한 다음 이미지를 파일로 저장할 수 있습니다(Blob 저장소에서도).

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

### <a name="relationships-among-table-object-and-file-projections"></a>테이블, 객체 및 파일 프로젝션 간의 관계

이 예제에서는 투영의 또 다른 기능도 강조 표시됩니다. 동일한 프로젝션 오브젝트 내에서 여러 유형의 프로젝션을 정의함으로써 서로 다른 유형(테이블, 개체, 파일)에 대해 표현되는 관계가 있으므로 문서의 테이블 행으로 시작하여 이미지의 모든 OCR 텍스트를 찾을 수 있습니다. 개체 프로젝션에서 해당 문서 내에서 

데이터와 관련되지 않도록 하려면 다른 투영 객체에서 투영을 정의합니다. 예를 들어 다음 코드 조각은 테이블과 OCR 텍스트(OCR 텍스트) 투영 간의 관계가 없는 테이블이 관련되는 결과를 초래합니다. 

프로젝션 그룹은 서로 다른 요구에 따라 동일한 데이터를 서로 다른 모양으로 투영하려는 경우에 유용합니다. 예를 들어 Power BI 대시보드용 프로젝션 그룹과 사용자 지정 기술로 래핑된 기계 학습 모델을 학습하는 데 사용되는 데이터를 캡처하는 다른 프로젝션 그룹입니다.

서로 다른 형식의 프로젝션을 작성할 때 파일 및 개체 투영이 먼저 생성되고 경로가 테이블에 추가됩니다.

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

프로젝션을 정의할 때 예기치 않은 결과를 초래할 수 있는 몇 가지 일반적인 문제가 있습니다. 지식 저장소의 출력이 예상과 다른 경우 이러한 문제를 확인합니다.

+ 문자열 보강을 유효한 JSON으로 형성하지 않습니다. 예를 들어 `merged_content` 키 구로 보강된 문자열이 보강되면 보강된 속성은 보강 `merged_content` 트리 내의 자식으로 표시됩니다. 기본 표현은 잘 형성되지 않습니다 JSON. 따라서 프로젝션 시 보강을 이름과 값이 있는 유효한 JSON 개체로 변환해야 합니다.

+ 소스 경로의 ```/*``` 끝에서 생략합니다. 프로젝션의 소스가 `/document/pbiShape/keyPhrases`인 경우 키 구 배열이 단일 개체/행으로 투영됩니다. 대신 각 키 구에 대해 단일 행 또는 개체를 생성하도록 `/document/pbiShape/keyPhrases/*` 소스 경로를 설정합니다.

+ 경로 구문 오류입니다. 경로 선택기는 대/소문자를 구분하며 선택기의 정확한 대/소문자를 사용하지 않으면 입력 경고가 누락될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 예제에서는 프로젝션을 만드는 방법에 대한 일반적인 패턴을 보여 줍니다. 이제 개념을 잘 이해하게 되었으므로 특정 시나리오에 대한 예측을 빌드하는 데 더 적합합니다.

새로운 기능을 탐색할 때 증분 보강을 다음 단계로 고려하십시오. 증분 보강은 캐싱을 기반으로 하므로 기술 집합 수정의 영향을 받지 않는 보강을 다시 사용할 수 있습니다. 이는 OCR 및 이미지 분석을 포함하는 파이프라인에 특히 유용합니다.

> [!div class="nextstepaction"]
> [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)

프로젝션에 대한 개요를 보려면 그룹 및 슬라이싱과 같은 기능 및 [기술 집합에서](knowledge-store-projection-overview.md) 프로젝션을 정의하는 방법에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [지식 저장소의 프로젝션](knowledge-store-projection-overview.md)

