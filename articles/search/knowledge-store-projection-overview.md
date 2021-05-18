---
title: 프로젝션 개념
titleSuffix: Azure Cognitive Search
description: AI 보강 인덱싱 파이프라인의 보강 데이터를 전체 텍스트 검색 이외의 시나리오에서 사용할 지식 저장소로 저장하고 도형을 저장합니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85565192"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소 "프로젝션"

Azure Cognitive Search을 사용하면 인덱싱의 일부로 기본 제공되는 인식 기술 및 사용자 지정 기술을 통해 콘텐츠를 보강할 수 있습니다. 보강은 이미지로부터 정보를 추출하고, 감정, 주요 구문을 탐지하며, 텍스트에서 엔티티를 찾아내고 일부를 이름 짓는 등 이전에는 존재하지 않았던 새로운 정보를 생성합니다. 보강은 구분되지 않은 텍스트에도 구조체를 추가합니다. 이러한 모든 프로세스는 전체 텍스트 검색을 보다 효율적으로 만드는 문서를 생성합니다. 대부분의 경우 보강 문서는 검색 이외의 시나리오 (예: 정보 마이닝)에 유용합니다.

[지식 저장소](knowledge-store-concept-intro.md)의 구성 요소인 프로젝션은 기술 자료를 위해 실제 스토리지에 저장할 수 있는 보강 문서에 대한 뷰입니다. 프로젝션을 사용하면 필요에 맞게 데이터를 "프로젝션"하여 Power BI 같은 도구에서 추가 작업 없이 데이터를 읽을 수 있도록 관계를 유지할 수 있습니다.

프로젝션은 Azure Table Storage의 행 및 열에 저장된 데이터 또는 Azure Blob Storage에 저장된 JSON 개체를 사용하여 테이블 형식으로 지정할 수 있습니다. 보강되는 데이터의 여러 프로젝션을 정의할 수 있습니다. 여러 프로젝션은 개별 사용 사례에 대해 동일한 데이터의 모양을 다르게 하려는 경우에 유용합니다.

지식 저장소는 세 가지 유형의 프로젝션을 지원합니다.

+ **테이블**: 행 및 열로 표현하는 것이 가장 적합한 데이터의 경우, 테이블 프로젝션을 통해 Table Storage에서 스키마화된 셰이프 또는 프로젝션을 정의할 수 있습니다. 유효한 JSON 개체만 테이블로 프로젝션될 수 있으며, 보강 문서는 JSON 개체가 아닌 노드를 포함하고 이러한 개체를 프로젝션하는 경우 쉐이퍼 기술 또는 인라인 셰이핑을 사용하여 유효한 JSON 개체를 만들 수 있습니다.

+ **개체**: 데이터 및 강화의 JSON 표현이 필요한 경우 개체 프로젝션이 Blob으로 저장됩니다. 유효한 JSON 개체만 개체로 프로젝션할 수 있으며, 보강된 문서에는 이름이 JSON 개체가 아닌 노드가 포함될 수 있으며, 이러한 개체를 프로젝션할 때 쉐이퍼 기술 또는 인라인 셰이핑을 사용하여 유효한 JSON 개체를 만듭니다.

+ **파일**: 문서에서 추출된 이미지를 저장해야 하는 경우, 파일 프로젝션을 사용하여 정규화된 이미지를 Blob Storage에 저장할 수 있습니다.

컨텍스트에서 정의된 프로젝션을 보려면 [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)를 단계별로 진행합니다.

## <a name="projection-groups"></a>프로젝션 그룹

경우에 따라 다양한 목표를 충족하도록 보강된 데이터를 다양한 셰이프로 프로젝션해야 합니다. 지식 저장소를 사용하면 여러 프로젝션 그룹을 정의할 수 있습니다. 프로젝션 그룹에는 상호 배타성 및 관련성의 다음과 같은 주요 특징이 있습니다.

### <a name="mutual-exclusivity"></a>상호 배제

단일 그룹으로 프로젝션된 모든 콘텐츠는 다른 프로젝션 그룹에 프로젝션된 데이터와 독립적입니다.
이러한 독립성 때문에 동일한 데이터의 모양이 달라지지만, 각 프로젝션 그룹에서 반복될 수 있습니다.

### <a name="relatedness"></a>관련성

이제 프로젝션 그룹을 사용하면 프로젝션 형식 간에 관계를 유지하면서 프로젝션 형식 간에 문서를 프로젝션할 수 있습니다. 단일 프로젝션 그룹 내에 프로젝션된 모든 콘텐츠는 프로젝션 형식 간에 데이터 내의 관계를 유지합니다. 테이블 내에서 관계는 생성된 키를 기반으로 하며, 각 자식 노드는 부모 노드에 대한 참조를 유지합니다. 여러 형식 (테이블, 개체 및 파일)에서 단일 노드가 서로 다른 형식으로 프로젝트될 때 관계가 유지됩니다. 예를 들어 이미지와 텍스트가 포함된 문서가 있는 시나리오를 생각해 보겠습니다. 테이블 또는 개체에 텍스트를 프로젝팅하고, 이미지를 테이블 또는 개체에 파일 URL을 포함하는 열/속성이 있는 파일에 프로젝팅할 수 있습니다.

## <a name="input-shaping"></a>입력 셰이핑

데이터를 올바른 모양 또는 구조로 얻는 것은 테이블 또는 개체와 같은 효과적인 사용을 위한 핵심입니다. 액세스 및 사용을 계획하는 방법에 따라 데이터를 모양 또는 구성하는 기능은 기술 세트 내에서 **쉐이퍼** 기술로 노출되는 주요 기능입니다.  

프로젝션의 스키마와 일치하는 보강 트리에 개체가 있는 경우 프로젝션을 정의하는 것이 더 쉽습니다. 업데이트된 [쉐이퍼 기술](cognitive-search-skill-shaper.md)을 사용하여 보강 트리의 여러 노드에서 개체를 작성하고 새 노드 아래에 부모를 지정할 수 있습니다. **쉐이퍼** 기술로 중첩된 개체를 사용하여 복합 형식을 정의할 수 있습니다.

프로젝트를 실행하는 데 필요한 모든 요소를 포함하는 새 셰이프를 정의하는 경우, 이제 이 셰이프를 프로젝션의 원본으로 사용하거나 다른 기술에 대한 입력으로 사용할 수 있습니다.

## <a name="projection-slicing"></a>프로젝션 조각화

프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 여러 개의 관련 테이블 혹은 개체로 분할할 수 있습니다. 기존 프로젝션의 자식인 원본 경로를 사용하여 프로젝션을 추가하면 자식 노드가 부모 노드에서 분리되고, 새로운 관련 테이블이나 개체로 프로젝션됩니다. 이러한 분할 기법을 사용하면 모든 테이블 프로젝션의 원본이 될 수 있는 단일 노드를 쉐이퍼 기술에 정의할 수 있습니다.

## <a name="table-projections"></a>테이블 프로젝션

가져오기를 용이하게 하므로 Power BI를 사용하여 데이터 탐색에 대한 테이블 프로젝션을 사용하는 것이 좋습니다. 또한 테이블 프로젝션을 사용하면 테이블 관계 간의 카디널리티를 변경할 수 있습니다. 

인덱스의 단일 문서를 여러 테이블에 프로젝션하여 관계를 유지할 수 있습니다. 여러 테이블로 프로젝션하는 경우, 자식 노드가 동일한 그룹 내에 있는 다른 테이블의 원본이 아니면 전체 셰이프가 각 테이블에 프로젝션됩니다.

### <a name="defining-a-table-projection"></a>테이블 프로젝션 정의

기술 세트의 `knowledgeStore` 요소 내에서 테이블 프로젝션을 정의하는 경우, 먼저 보강 트리의 노드를 테이블 원본에 매핑합니다. 일반적으로 이 노드는 테이블에 프로젝션해야 하는 특정 셰이프를 생성하는 기술 목록에 추가된 **쉐이퍼** 기술의 출력입니다. 프로젝트를 선택하여 프로젝트를 여러 테이블로 분할할 수 있습니다. 테이블 정의는 프로젝트하려는 테이블의 목록입니다.

각 테이블에는 세 가지 속성이 필요합니다.

+ tableName: Azure Storage에 있는 테이블의 이름입니다.

+ generatedKeyName: 이 행을 고유하게 식별하는 키의 열 이름.

+ source: 보강을 소싱할 보강 트리의 노드입니다. 이 노드는 일반적으로 쉐이퍼의 출력이지만 모든 기술의 출력일 수 있습니다.

테이블 프로젝션의 예는 다음과 같습니다.

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

이 예제에서 설명한 것처럼 핵심 구와 엔터티는 서로 다른 테이블로 모델링되며 각 행에 대한 부모(MainTable)에 대한 참조를 포함합니다.

## <a name="object-projections"></a>개체 프로젝션

개체 프로젝션은 모든 노드에서 원본으로 사용할 수 있는 보강 트리의 JSON 표현입니다. 대부분의 경우 테이블 프로젝션을 만드는 동일한 **쉐이퍼** 기술을 사용하여 개체 프로젝션을 생성할 수 있습니다. 

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

개체 프로젝션을 생성하려면 몇 가지 개체별 특성이 필요합니다.

+ storageContainer: 개체가 저장될 blob 컨테이너
+ source: 프로젝션의 루트인 보강 트리의 노드 경로.

## <a name="file-projection"></a>파일 프로젝션

파일 프로젝션은 개체 프로젝션과 유사하며 `normalized_images` 컬렉션에서만 작동합니다. 개체 프로젝션과 마찬가지로 파일 프로젝션은 문서 ID의 base64로 인코딩된 값의 폴더 접두사를 사용하여 Blob 컨테이너에 저장됩니다. 파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으며, 다른 컨테이너에 프로젝션되어야 합니다.

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

프로젝션에는 데이터 원본의 원본 데이터에 연결된 수명 주기가 있습니다. 데이터가 업데이트되고 다시 인덱싱되면, 프로젝션이 보강 결과로 업데이트되어 프로젝션이 결국 데이터 원본의 데이터와 일치하도록 합니다. 프로젝션은 인덱스에 대해 구성한 삭제 정책을 상속합니다. 인덱서 또는 검색 서비스 자체가 삭제되면 프로젝션이 삭제되지 않습니다.

## <a name="using-projections"></a>프로젝션 사용

인덱서를 실행한 후에는 프로젝션을 통해 지정한 컨테이너 또는 테이블에서 프로젝션된 데이터를 읽을 수 있습니다.

분석의 경우 Power BI의 탐색은 Azure Table Storage를 데이터 원본으로 설정하는 것처럼 간단합니다. 내에서 관계를 사용하여 데이터에 대한 시각화 집합을 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인에서 보강 데이터를 사용해야 하는 경우 [blob의 데이터를 Pandas DataFrame에 로드할](../machine-learning/team-data-science-process/explore-data-blob.md) 수 있습니다.

마지막으로 지식 저장소에서 데이터를 내보내야 하는 경우, Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계로 샘플 데이터 및 지침을 사용하여 첫 번째 지식 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)

조각화, 인라인 셰이핑 및 관계와 같은 고급 투영 개념에 대한 자습서에서는 [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)를 시작합니다.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)
