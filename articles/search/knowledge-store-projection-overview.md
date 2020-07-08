---
title: 프로젝션 개념
titleSuffix: Azure Cognitive Search
description: AI 보강 인덱싱 파이프라인의 보강 데이터를 전체 텍스트 검색 이외의 시나리오에서 사용할 정보 저장소로 저장 하 고 셰이프를 저장 합니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565192"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search의 기술 자료 저장소 "프로젝션"

Azure Cognitive Search을 사용 하면 인덱싱의 일부로 기본 제공 되는 인식 기술 및 사용자 지정 기술을 통해 콘텐츠를 보강 수 있습니다. 강화 이미지에서 정보를 추출 하 고, 감정, 키 구 및 텍스트에서 엔터티를 검색 하 고, 이름을 몇 개로 하 여 새 정보를 만듭니다. 강화는 구분 되지 않은 텍스트에도 구조체를 추가 합니다. 이러한 모든 프로세스는 전체 텍스트 검색을 보다 효율적으로 만드는 문서를 생성 합니다. 대부분의 경우 보강 문서는 검색 이외의 시나리오 (예: 기술 자료 마이닝)에 유용 합니다.

[기술 자료 저장소](knowledge-store-concept-intro.md)의 구성 요소인 프로젝션은 기술 자료를 위해 실제 저장소에 저장할 수 있는 보강 문서에 대 한 뷰입니다. 프로젝션을 사용 하면 필요에 맞게 데이터를 "프로젝션" 하 여 Power BI 같은 도구에서 추가 작업 없이 데이터를 읽을 수 있도록 관계를 유지할 수 있습니다.

프로젝션은 Azure Table storage의 행 및 열에 저장 된 데이터 또는 Azure Blob storage에 저장 된 JSON 개체를 사용 하 여 테이블 형식으로 지정할 수 있습니다. 보강 되는 데이터의 여러 프로젝션을 정의할 수 있습니다. 여러 프로젝션은 개별 사용 사례에 대해 동일한 데이터 모양을 다르게 하려는 경우에 유용 합니다.

기술 자료 저장소는 세 가지 유형의 프로젝션을 지원 합니다.

+ **테이블**: 행 및 열로 가장 잘 표현 되는 데이터의 경우 테이블 프로젝션을 사용 하 여 테이블 저장소에서 스키마 화 된 셰이프 또는 프로젝션을 정의할 수 있습니다. 유효한 JSON 개체만 테이블로 프로젝션 될 수 있으며, 보강 문서는 JSON 개체가 아닌 노드를 포함 하 고 이러한 개체를 프로젝션 하는 경우 shaper 기술 또는 인라인 셰이핑을 사용 하 여 유효한 JSON 개체를 만들 수 있습니다.

+ **개체**: 데이터 및 강화의 JSON 표현이 필요한 경우 개체 프로젝션이 blob으로 저장 됩니다. 유효한 JSON 개체만 개체로 프로젝션 될 수 있으며, 보강 문서는 JSON 개체가 아닌 노드를 포함 하 고 이러한 개체를 프로젝션 하는 경우 shaper 기술 또는 인라인 셰이핑을 사용 하 여 유효한 JSON 개체를 만들 수 있습니다.

+ **파일**: 문서에서 추출한 이미지를 저장 해야 하는 경우 파일 프로젝션을 통해 정규화 된 이미지를 blob 저장소에 저장할 수 있습니다.

컨텍스트에 정의 된 프로젝션을 보려면 [REST에서 기술 자료 저장소 만들기](knowledge-store-create-rest.md)를 단계별로 진행 합니다.

## <a name="projection-groups"></a>프로젝션 그룹

경우에 따라 서로 다른 목표를 충족 하기 위해 보강 데이터를 다른 모양으로 프로젝션 해야 하는 경우도 있습니다. 기술 자료 저장소를 사용 하면 여러 개의 프로젝션 그룹을 정의할 수 있습니다. 프로젝션 그룹에는 상호 독점 성을 및 관련성의 다음과 같은 주요 특징이 있습니다.

### <a name="mutual-exclusivity"></a>상호 독점 성을

단일 그룹으로 프로젝션 된 모든 콘텐츠는 다른 프로젝션 그룹으로 프로젝션 된 데이터와는 독립적입니다.
이러한 독립성은 동일한 데이터를 서로 다른 모양으로 표시 하 고 각 프로젝션 그룹에서 반복할 수 있음을 의미 합니다.

### <a name="relatedness"></a>관련성

이제 프로젝션 그룹을 사용 하 여 프로젝션 형식 간에 관계를 유지 하면서도 프로젝션 형식 간에 문서를 투영할 수 있습니다. 단일 프로젝션 그룹 내에서 프로젝션 된 모든 콘텐츠는 프로젝션 형식 간에 데이터 내에서 관계를 유지 합니다. 테이블 내에서 관계는 생성 된 키를 기반으로 하며 각 자식 노드는 부모 노드에 대 한 참조를 유지 합니다. 형식 (테이블, 개체 및 파일) 간에는 단일 노드가 여러 형식에서 프로젝션 될 때 관계가 유지 됩니다. 예를 들어 이미지와 텍스트가 포함 된 문서가 있는 시나리오를 가정해 보겠습니다. 테이블이 나 개체 및 테이블에 파일 URL을 포함 하는 열/속성을 포함 하는 파일에 텍스트를 프로젝션 할 수 있습니다.

## <a name="input-shaping"></a>입력 셰이핑

올바른 모양 또는 구조에서 데이터를 가져오는 것은 테이블이 나 개체 일 수 있는 효과적인 용도입니다. 액세스 및 사용을 계획 하는 방법에 따라 데이터를 모양 또는 구성 하는 기능은 기술 내에서 **Shaper** 기술로 노출 되는 주요 기능입니다.  

프로젝션의 스키마와 일치 하는 보강 트리에 개체가 있는 경우 프로젝션을 정의 하는 것이 더 쉽습니다. 업데이트 된 [Shaper 기술을](cognitive-search-skill-shaper.md) 사용 하 여 보강 트리의 여러 노드에서 개체를 작성 하 고 새 노드 아래에 부모를 지정할 수 있습니다. **Shaper** 기술로 중첩 된 개체를 사용 하 여 복합 형식을 정의할 수 있습니다.

프로젝트를 실행 하는 데 필요한 모든 요소를 포함 하는 새 셰이프를 정의 하는 경우 이제이 셰이프를 프로젝션의 소스로 사용 하거나 다른 기술에 대 한 입력으로 사용할 수 있습니다.

## <a name="projection-slicing"></a>프로젝션 조각화

프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 관련 된 여러 테이블 또는 개체로 분할할 수 있습니다. 기존 프로젝션의 자식인 원본 경로를 사용 하 여 프로젝션을 추가 하면 자식 노드가 부모 노드에서 분리 되 고 새로운 관련 테이블이 나 개체로 프로젝션 됩니다. 이 기술을 사용 하면 모든 프로젝션의 소스가 될 수 있는 shaper 기술에서 단일 노드를 정의할 수 있습니다.

## <a name="table-projections"></a>테이블 프로젝션

가져오기를 용이 하 게 하므로 Power BI를 사용 하 여 데이터 탐색에 대 한 테이블 프로젝션을 사용 하는 것이 좋습니다. 또한 테이블 프로젝션을 사용 하면 테이블 관계 간의 카디널리티를 변경할 수 있습니다. 

인덱스의 단일 문서를 여러 테이블에 프로젝션 하 여 관계를 유지할 수 있습니다. 여러 테이블로 프로젝션 하는 경우 자식 노드가 동일한 그룹 내에 있는 다른 테이블의 원본이 아니면 전체 셰이프가 각 테이블에 투영 됩니다.

### <a name="defining-a-table-projection"></a>테이블 프로젝션 정의

기술의 요소 내에서 테이블 프로젝션을 정의 하는 경우 `knowledgeStore` 먼저 보강 트리의 노드를 테이블 원본에 매핑합니다. 일반적으로이 노드는 테이블에 프로젝션 해야 하는 특정 셰이프를 생성 하는 기술 목록에 추가 된 **Shaper** 기술의 출력입니다. 프로젝트를 선택 하 여 프로젝트를 여러 테이블로 분할할 수 있습니다. 테이블 정의는 프로젝트 하려는 테이블의 목록입니다.

각 테이블에는 세 가지 속성이 필요 합니다.

+ tableName: Azure Storage에 있는 테이블의 이름입니다.

+ generatedKeyName:이 행을 고유 하 게 식별 하는 키의 열 이름입니다.

+ 원본: 강화를 소싱 하는 보강 트리의 노드입니다. 일반적으로이 노드는 shaper의 출력 이지만 모든 기술의 출력이 될 수 있습니다.

표 프로젝션의 예는 다음과 같습니다.

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

이 예제에서 설명한 것 처럼 키 구와 엔터티는 서로 다른 테이블로 모델링 되며 각 행에 대 한 부모 (MainTable)에 대 한 참조를 포함 합니다.

## <a name="object-projections"></a>개체 프로젝션

개체 프로젝션은 모든 노드에서 원본으로 사용할 수 있는 보강 트리의 JSON 표현입니다. 대부분의 경우 테이블 프로젝션을 만드는 동일한 **Shaper** 기술을 사용 하 여 개체 프로젝션을 생성할 수 있습니다. 

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

개체 프로젝션을 생성 하려면 몇 가지 개체 관련 특성이 필요 합니다.

+ storageContainer: 개체가 저장 될 blob 컨테이너입니다.
+ source: 프로젝션의 루트인 보강 트리의 노드에 대 한 경로입니다.

## <a name="file-projection"></a>파일 프로젝션

파일 프로젝션은 개체 프로젝션과 비슷하며 컬렉션 에서만 작동 `normalized_images` 합니다. 개체 프로젝션과 마찬가지로 파일 프로젝션은 blob 컨테이너에 문서 ID의 base64 인코딩 값의 폴더 접두사로 저장 됩니다. 파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으며 다른 컨테이너에 프로젝션 해야 합니다.

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

프로젝션에는 데이터 원본의 원본 데이터와 연결 되는 수명 주기가 있습니다. 데이터가 업데이트 되 고 인덱싱해야 되 면 예측은 궁극적으로 데이터 원본의 데이터와 일치 하는지 확인 하는 강화의 결과로 업데이트 됩니다. 프로젝션은 인덱스에 대해 구성한 삭제 정책을 상속 합니다. 인덱서 또는 검색 서비스 자체가 삭제 된 경우에는 프로젝션이 삭제 되지 않습니다.

## <a name="using-projections"></a>프로젝션 사용

인덱서를 실행 한 후에는 프로젝션을 통해 지정한 컨테이너 또는 테이블에서 프로젝션 된 데이터를 읽을 수 있습니다.

분석의 경우 Power BI의 탐색은 Azure Table storage를 데이터 원본으로 설정 하는 것 처럼 간단 합니다. 내에서 관계를 사용 하 여 데이터에 대 한 시각화 집합을 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인에서 보강 데이터를 사용 해야 하는 경우 [blob의 데이터를 Pandas 데이터 프레임에 로드할](../machine-learning/team-data-science-process/explore-data-blob.md)수 있습니다.

마지막으로 기술 자료 저장소에서 데이터를 내보내야 하는 경우 Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치 하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계로 샘플 데이터 및 지침을 사용 하 여 첫 번째 정보 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [REST에서 기술 자료 저장소를 만듭니다](knowledge-store-create-rest.md).

조각화, 인라인 셰이핑 및 관계와 같은 고급 투영 개념에 대한 자습서에서는 [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)를 시작합니다.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)
