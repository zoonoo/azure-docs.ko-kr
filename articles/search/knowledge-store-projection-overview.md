---
title: 기술 자료 저장소에서 프로젝션 작업 (미리 보기)-Azure Search
description: 검색 이외의 시나리오에서 사용할 보강 데이터를 AI 인덱싱 파이프라인에서 저장 하 고 셰이프를 저장 합니다.
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.subservice: cognitive-search
ms.openlocfilehash: 2dd61a4511d406fefec5aacd0702fa732f79de92
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186240"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Azure Search의 기술 자료 저장소에서 프로젝션 사용

> [!Note]
> 지식 저장소는 미리 보기로 제공되며 프로덕션 용도로는 제공되지 않습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>

Azure Search는 인덱싱의 일부로 AI 인식 기술 및 사용자 지정 기술을 통해 콘텐츠 보강을 가능 하 게 합니다. 강화를 사용 하 여 문서에 구조를 추가 하 고 검색을 더 효과적으로 수행할 수 있습니다. 대부분의 경우 보강 문서는 검색 이외의 시나리오 (예: 기술 자료 마이닝)에 유용 합니다.

[기술 자료 저장소](knowledge-store-concept-intro.md)의 구성 요소인 프로젝션은 기술 자료를 위해 실제 저장소에 저장할 수 있는 보강 문서에 대 한 뷰입니다. 프로젝션을 사용 하면 필요에 맞게 데이터를 "프로젝션" 하 여 Power BI 같은 도구에서 추가 작업 없이 데이터를 읽을 수 있도록 관계를 유지할 수 있습니다. 

프로젝션은 Azure Table storage의 행 및 열에 저장 된 데이터 또는 Azure Blob storage에 저장 된 JSON 개체를 사용 하 여 테이블 형식으로 지정할 수 있습니다. 보강 되는 데이터의 여러 프로젝션을 정의할 수 있습니다. 이는 개별 사용 사례에 대해 동일한 데이터 모양을 다르게 하려는 경우에 유용 합니다. 

지식 저장소는 두 가지 유형의 프로젝션을 지원 합니다.

+ **테이블**: 행과 열로 가장 잘 표현 되는 데이터의 경우 테이블 프로젝션을 사용 하 여 테이블 저장소에서 스키마 화 된 셰이프 또는 프로젝션을 정의할 수 있습니다. 

+ **개체**: 데이터 및 강화의 JSON 표현이 필요한 경우 개체 프로젝션이 blob으로 저장 됩니다.

컨텍스트에 정의 된 프로젝션을 보려면 [기술 자료 저장소를 시작 하는 방법을](knowledge-store-howto.md) 단계별로 안내 합니다.

## <a name="projection-groups"></a>프로젝션 그룹

경우에 따라 서로 다른 목표를 충족 하기 위해 보강 데이터를 다른 모양으로 프로젝션 해야 하는 경우도 있습니다. 기술 자료 저장소를 사용 하면 여러 개의 프로젝션 그룹을 정의할 수 있습니다. 프로젝션 그룹에는 상호 독점 성을 및 관련성의 다음과 같은 주요 특징이 있습니다.

### <a name="mutually-exclusivity"></a>상호 독점 성을

단일 그룹으로 프로젝션 된 모든 콘텐츠는 다른 프로젝션 그룹으로 프로젝션 된 데이터와는 독립적입니다. 즉, 각 프로젝션 그룹에서 반복 되는 동일한 데이터를 사용할 수 있습니다. 

프로젝션 그룹에 적용 되는 한 가지 제약 조건은 프로젝션 그룹을 사용 하는 프로젝션 형식의 상호 독점 성을. 단일 그룹 내에서 테이블 프로젝션 또는 개체 프로젝션을 정의할 수 있습니다. 테이블과 개체를 모두 원하는 경우 테이블에 대해 하나의 프로젝션 그룹을 정의 하 고 개체에 대 한 두 번째 프로젝션 그룹을 정의 합니다.

### <a name="relatedness"></a>관련성

단일 프로젝션 그룹 내에서 프로젝션 된 모든 콘텐츠는 데이터 내에서 관계를 유지 합니다. 관계는 생성 된 키를 기반으로 하며 각 자식 노드는 부모 노드에 대 한 참조를 유지 합니다. 관계는 프로젝션 그룹에 속하지 않으며, 한 프로젝션 그룹에서 만들어진 테이블 또는 개체는 다른 프로젝션 그룹에 생성 된 데이터와 관계가 없습니다.

## <a name="input-shaping"></a>입력 셰이핑
올바른 모양 또는 구조에서 데이터를 가져오는 것은 테이블이 나 개체 일 수 있는 효과적인 용도입니다. 액세스 및 사용을 계획 하는 방법에 따라 데이터를 모양 또는 구성 하는 기능은 기술 내에서 **Shaper** 기술로 노출 되는 주요 기능입니다.  

프로젝션의 스키마와 일치 하는 보강 트리에 개체가 있는 경우 프로젝션을 정의 하는 것이 더 쉽습니다. 업데이트 된 [Shaper 기술을](cognitive-search-skill-shaper.md) 사용 하 여 보강 트리의 여러 노드에서 개체를 작성 하 고 새 노드 아래에 부모를 지정할 수 있습니다. **Shaper** 기술로 중첩 된 개체를 사용 하 여 복합 형식을 정의할 수 있습니다.

프로젝트를 실행 하는 데 필요한 모든 요소를 포함 하는 새 셰이프를 정의 하는 경우 이제이 셰이프를 프로젝션의 소스로 사용 하거나 다른 기술에 대 한 입력으로 사용할 수 있습니다.

## <a name="table-projections"></a>테이블 프로젝션

가져오기를 용이 하 게 하므로 Power BI를 사용 하 여 데이터 탐색에 대 한 테이블 프로젝션을 사용 하는 것이 좋습니다. 또한 테이블 프로젝션을 사용 하면 테이블 관계 간의 카디널리티 변경을 허용할 수 있습니다. 

인덱스의 단일 문서를 여러 테이블에 프로젝션 하 여 관계를 유지할 수 있습니다. 여러 테이블로 프로젝션 하는 경우 자식 노드가 동일한 그룹 내에 있는 다른 테이블의 원본이 아니면 전체 셰이프가 각 테이블에 투영 됩니다.

### <a name="defining-a-table-projection"></a>테이블 프로젝션 정의

기술의 `knowledgeStore` 요소 내에서 테이블 프로젝션을 정의 하는 경우 먼저 보강 트리의 노드를 테이블 원본에 매핑합니다. 일반적으로이 노드는 테이블에 프로젝션 해야 하는 특정 셰이프를 생성 하는 기술 목록에 추가 된 **Shaper** 기술의 출력입니다. 프로젝트를 선택 하 여 프로젝트를 여러 테이블로 분할할 수 있습니다. 테이블 정의는 프로젝트 하려는 테이블의 목록입니다. 

각 테이블에는 세 가지 속성이 필요 합니다.

+ tableName: Azure Storage에 있는 테이블의 이름입니다.

+ generatedKeyName: 이 행을 고유 하 게 식별 하는 키의 열 이름입니다.

+ 원본: 강화에서 소싱 한 보강 트리의 노드입니다. 이는 일반적으로 shaper의 출력 이지만 모든 기술의 출력이 될 수 있습니다.

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
이 예제에서 설명한 것 처럼 키 구와 엔터티는 서로 다른 테이블로 모델링 되며 각 행에 대 한 부모 (MainTable)에 대 한 참조를 포함 합니다. 

다음 그림은 [기술 자료 저장소를 시작 하는 방법](knowledge-store-howto.md)의 Caselaw 연습에 대 한 참조입니다. 대/소문자에 여러 개의 의견이 있는 시나리오에서 각 의견에 포함 된 엔터티를 식별 하 여 각 의견을 보강 여기에 표시 된 대로 프로젝션을 모델링할 수 있습니다.

![테이블의 엔터티 및 관계](media/knowledge-store-projection-overview/TableRelationships.png "테이블 프로젝션에서 관계 모델링")

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

개체 프로젝션을 생성 하려면 몇 가지 개체 관련 특성이 필요 합니다.

+ storageContainer: 개체가 저장 될 컨테이너입니다.
+ 원본: 프로젝션의 루트인 보강 트리의 노드에 대 한 경로입니다.
+ 키인지 저장할 개체의 고유 키를 나타내는 경로입니다. 컨테이너에서 blob의 이름을 만드는 데 사용 됩니다.

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

프로젝션에는 데이터 원본의 원본 데이터와 연결 되는 수명 주기가 있습니다. 데이터가 업데이트 되 고 다시 인덱싱되 면 프로젝션이 데이터 원본의 데이터와 일치 하는지 확인 하는 강화 결과가 업데이트 됩니다. 프로젝션은 인덱스에 대해 구성한 삭제 정책을 상속 합니다. 

## <a name="using-projections"></a>프로젝션 사용

인덱서를 실행 한 후에는 프로젝션을 통해 지정한 컨테이너 또는 테이블에서 프로젝션 된 데이터를 읽을 수 있습니다. 

분석의 경우 Power BI의 탐색은 Azure Table storage를 데이터 원본으로 설정 하는 것 처럼 간단 합니다. 내에서 관계를 활용 하 여 데이터에 대 한 시각화 집합을 매우 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인에서 보강 데이터를 사용 해야 하는 경우 [blob의 데이터를 Pandas 데이터 프레임에 로드할](../machine-learning/team-data-science-process/explore-data-blob.md)수 있습니다.

마지막으로 기술 자료 저장소에서 데이터를 내보내야 하는 경우 Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치 하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계로 샘플 데이터 및 지침을 사용 하 여 첫 번째 정보 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [기술 자료 저장소를 만드는 방법](knowledge-store-howto.md)