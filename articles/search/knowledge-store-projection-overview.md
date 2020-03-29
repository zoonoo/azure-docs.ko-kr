---
title: 지식 저장소의 프로젝션(미리 보기)
titleSuffix: Azure Cognitive Search
description: AI 보강 인덱싱 파이프라인에서 전체 텍스트 검색 이외의 시나리오에서 사용할 수 있는 지식 저장소로 보강된 데이터를 저장하고 셰이핑합니다. 지식 저장소는 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942981"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Azure 인지 검색의 지식 저장소의 프로젝션

> [!IMPORTANT] 
> 지식 저장소는 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-Preview](search-api-preview.md)는 미리 보기 기능을 제공합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

Azure Cognitive Search를 사용하면 인덱싱의 일부로 기본 제공 인지 기술 및 사용자 지정 기술을 통해 콘텐츠를 보강할 수 있습니다. 보강은 이미지에서 정보를 추출하고, 감정, 핵심 구 및 엔터티를 텍스트에서 감지하는 등 이전에는 존재하지 않았던 새로운 정보를 생성합니다. 보강은 또한 차별화되지 않은 텍스트에 구조를 추가합니다. 이러한 모든 프로세스는 전체 텍스트 검색을 보다 효과적으로 만드는 문서로 이됩니다. 대부분의 경우 보강된 문서는 지식 마이닝과 같은 검색 이외의 시나리오에 유용합니다.

[지식 저장소의](knowledge-store-concept-intro.md)구성 요소인 프로젝션은 지식 마이닝을 위해 물리적 저장소에 저장할 수 있는 보강된 문서의 보기입니다. 프로젝션을 사용하면 데이터를 필요에 맞는 모양으로 "투영"하여 관계를 유지하여 Power BI와 같은 도구가 추가 노력 없이 데이터를 읽을 수 있도록 합니다.

프로젝션은 Azure 테이블 저장소의 행 과 열에 저장된 데이터 또는 Azure Blob 저장소에 저장된 JSON 개체와 함께 테이블 형식일 수 있습니다. 데이터가 보강되는 동안 데이터의 여러 투영을 정의할 수 있습니다. 여러 프로젝션은 개별 사용 사례에 대해 동일한 데이터 모양을 다르게 지정하려는 경우에 유용합니다.

지식 저장소는 세 가지 유형의 프로젝션을 지원합니다.

+ **표**: 행과 열로 가장 잘 표현되는 데이터의 경우 테이블 프로젝션을 사용하면 테이블 저장소에서 스키마화된 셰이프 또는 프로젝션을 정의할 수 있습니다. 유효한 JSON 개체만 테이블로 프로젝션할 수 있으며, 보강된 문서에는 JSON 개체라는 이름이 지정되지 않은 노드가 포함될 수 있으며 이러한 개체를 프로젝션할 때 셰이퍼 기술 또는 인라인 셰이핑을 사용하여 유효한 JSON 개체를 만들 수 있습니다.

+ **개체**: 데이터 및 보강에 대한 JSON 표현이 필요한 경우 개체 투영이 Blob으로 저장됩니다. 유효한 JSON 개체만 객체로 투영할 수 있으며, 보강된 문서에는 JSON 개체라는 이름이 지정되지 않은 노드가 포함될 수 있으며 이러한 개체를 프로젝션할 때 셰이퍼 기술 또는 인라인 셰이핑을 사용하여 유효한 JSON 오브젝트를 만들 수 있습니다.

+ **파일**: 문서에서 추출 한 이미지를 저장해야하는 경우 파일 프로젝션을 사용하면 정규화된 이미지를 Blob 저장소에 저장할 수 있습니다.

컨텍스트에서 정의된 프로젝션을 보려면 [REST에서 기술 저장소 만들기를](knowledge-store-create-rest.md)단계별로 살펴보겠습니다.

## <a name="projection-groups"></a>프로젝션 그룹

경우에 따라 서로 다른 목표를 충족하기 위해 보강된 데이터를 다른 모양으로 투영해야 합니다. 지식 저장소를 사용하면 여러 투영 그룹을 정의할 수 있습니다. 프로젝션 그룹은 상호 독점및 관련성의 다음과 같은 주요 특성을 갖는다.

### <a name="mutual-exclusivity"></a>상호 배타성

단일 그룹에 투영된 모든 콘텐츠는 다른 프로젝션 그룹에 투영된 데이터와 독립적입니다.
이 독립성은 동일한 데이터의 모양을 다르게 만들 수 있지만 각 프로젝션 그룹에서 반복될 수 있음을 의미합니다.

### <a name="relatedness"></a>Relatedness

이제 프로젝션 그룹을 사용하면 프로젝션 유형 간에 문서를 투영하는 동시에 프로젝션 유형 간 관계를 유지할 수 있습니다. 단일 프로젝션 그룹 내에 투영된 모든 콘텐츠는 프로젝션 유형 간에 데이터 내에서 관계를 유지합니다. 테이블 내에서 관계는 생성된 키를 기반으로 하며 각 자식 노드는 상위 노드에 대한 참조를 유지합니다. 형식(테이블, 개체 및 파일)에서 단일 노드가 여러 유형에 걸쳐 투영될 때 관계가 유지됩니다. 예를 들어 이미지와 텍스트가 포함된 문서가 있는 시나리오를 생각해 보십시오. 텍스트를 테이블이나 개체에 투영하고 테이블을 투영할 수 있으며 테이블이나 개체에 파일 URL이 포함된 열/속성이 있는 파일에 이미지를 투영할 수 있습니다.

## <a name="input-shaping"></a>입력 쉐이핑

올바른 모양이나 구조로 데이터를 가져오는 것은 테이블이나 개체와 같은 효과적인 사용의 핵심입니다. 데이터에 액세스하고 사용할 계획에 따라 데이터를 구성하거나 구성하는 기능은 기술 집합 내에서 **Shaper** 기술로 노출된 핵심 기능입니다.  

프로젝션의 스키마와 일치하는 보강 트리에 개체가 있는 경우 프로젝션을 더 쉽게 정의할 수 있습니다. 업데이트된 [Shaper 기술을](cognitive-search-skill-shaper.md) 사용하면 보강 트리의 여러 노드에서 개체를 구성하고 새 노드에서 모를 수 있습니다. **셰이퍼** 기술을 사용하면 중첩된 개체를 사용하여 복잡한 형식을 정의할 수 있습니다.

투영에 필요한 모든 요소를 포함하는 새 셰이프가 정의된 경우 이제 이 셰이프를 프로젝션의 소스또는 다른 기술에 대한 입력으로 사용할 수 있습니다.

## <a name="projection-slicing"></a>프로젝션 슬라이싱

프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 여러 관련 테이블 또는 개체로 분할할 수 있습니다. 기존 프로젝션의 자식인 소스 경로가 있는 프로젝션을 추가하면 자식 노드가 부모 노드에서 분할되어 새 아직 관련 테이블 또는 개체에 투영됩니다. 이 기술을 사용하면 모든 프로젝션의 소스가 될 수 있는 셰이퍼 기술의 단일 노드를 정의할 수 있습니다.

## <a name="table-projections"></a>테이블 프로젝션

가져오기가 더 쉬워지므로 Power BI를 사용하여 데이터 탐색을 위한 테이블 프로젝션을 권장합니다. 또한 테이블 프로젝션을 사용하면 테이블 관계 간의 카디널리티를 변경할 수 있습니다. 

인덱스의 단일 문서를 여러 테이블에 투영하여 관계를 유지할 수 있습니다. 여러 테이블에 프로젝션할 때 자식 노드가 동일한 그룹 내의 다른 테이블의 소스가 아니면 전체 셰이프가 각 테이블에 투영됩니다.

### <a name="defining-a-table-projection"></a>테이블 투영 정의

기술 집합 요소 내에서 `knowledgeStore` 테이블 프로젝션을 정의할 때 보강 트리의 노드를 테이블 소스에 매핑하여 시작합니다. 일반적으로 이 노드는 테이블에 투영해야 하는 특정 셰이프를 생성하기 위해 기술 목록에 추가한 **Shaper** 기술의 출력입니다. 프로젝트로 선택한 노드를 슬라이스하여 여러 테이블로 투영할 수 있습니다. 테이블 정의는 프로젝세이할 테이블 목록입니다.

각 테이블에는 다음 세 가지 속성이 필요합니다.

+ 테이블 이름: Azure 저장소의 테이블 이름입니다.

+ 생성된KeyName: 이 행을 고유하게 식별하는 키의 열 이름입니다.

+ 소스: 농축 트리의 노드에서 농축을 조달합니다. 이 노드는 일반적으로 셰이퍼의 출력이지만 모든 기술의 출력일 수 있습니다.

다음은 테이블 프로젝션의 예입니다.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

이 예제에서 설명한 것처럼 키 구 및 엔터티는 서로 다른 테이블로 모델링되며 각 행에 대한 상위(MainTable)에 대한 참조를 포함합니다.

## <a name="object-projections"></a>개체 투영

개체 투영은 모든 노드에서 소스할 수 있는 보강 트리의 JSON 표현입니다. 대부분의 경우 테이블 **프로젝션을** 생성하는 셰이퍼 기술을 사용하여 개체 프로젝션을 생성할 수 있습니다. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

개체 프로젝션을 생성하려면 다음과 같은 몇 가지 개체별 특성이 필요합니다.

+ storageContainer: 개체가 저장될 Blob 컨테이너
+ 소스: 프로젝션의 루트인 보강 트리의 노드에 대한 경로

## <a name="file-projection"></a>파일 프로젝션

파일 프로젝션은 개체 투영과 유사하며 `normalized_images` 컬렉션에만 작동합니다. 개체 프로젝션과 마찬가지로 파일 프로젝션은 문서 ID의 base64 인코딩된 값의 폴더 접두사가 있는 Blob 컨테이너에 저장됩니다. 파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으므로 다른 컨테이너에 투영해야 합니다.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

예측에는 데이터 원본의 원본 데이터와 연결된 수명 주기가 있습니다. 데이터가 업데이트되고 다시 인덱싱되면 프로젝션이 보강 결과로 업데이트되어 예측이 결국 데이터 원본의 데이터와 일치하도록 합니다. 프로젝션은 인덱스에 대해 구성한 삭제 정책을 상속합니다. 인덱서 또는 검색 서비스 자체가 삭제되면 프로젝션이 삭제되지 않습니다.

## <a name="using-projections"></a>프로젝션 사용

인덱서를 실행한 후 프로젝션을 통해 지정한 컨테이너 또는 테이블에서 투영된 데이터를 읽을 수 있습니다.

분석의 경우 Power BI에서 탐색하는 것은 Azure Table 저장소를 데이터 원본으로 설정하는 것만큼 간단합니다. 내 관계를 사용하여 데이터에 시각화 집합을 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인에서 보강된 데이터를 사용해야 하는 경우 [Blob의 데이터를 Pandas DataFrame에 로드할](../machine-learning/team-data-science-process/explore-data-blob.md)수 있습니다.

마지막으로 지식 저장소에서 데이터를 내보내야 하는 경우 Azure Data Factory에는 데이터를 내보내고 원하는 데이터베이스에 데이터를 저장하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계로 샘플 데이터 및 지침을 사용하여 첫 번째 지식 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [REST.](knowledge-store-create-rest.md)

슬라이싱, 인라인 형성 및 관계와 같은 고급 프로젝션 개념을 다루는 자습서의 경우 [지식 저장소에서 프로젝션 정의로](knowledge-store-projections-examples.md) 시작하십시오.

> [!div class="nextstepaction"]
> [지식 저장소에서 투영 정의](knowledge-store-projections-examples.md)
