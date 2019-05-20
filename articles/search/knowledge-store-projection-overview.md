---
title: 정보 저장소 (미리 보기)-Azure Search에에서는 프로젝션과 함께 작업
description: 저장 및 검색 이외의 시나리오에서 사용 하기 위해 AI 인덱싱 파이프라인에서 풍부한 데이터를 구체화
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: f1c7278909557dc92f86c5dfc1f190fddf33f607
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540816"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Azure Search 기술 저장소에는 프로젝션과 함께 작업

> [!Note]
> 저장소 기술 미리 보기로 제공 되며 프로덕션 사용에 대 한 용도 아니지만 경우 합니다 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md) 이 기능을 제공 합니다. 이 이번에는.NET SDK 지원은 없습니다.
>

Azure Search에 AI cognitive 기술과 인덱싱의 일부로 사용자 지정 기술 콘텐츠 보강할 수 있습니다. 강화를 문서 구조를 추가 및 검색 하는 것 보다 효율적입니다. 많은 경우 풍부한 문서 검색, 예: 기술 마이닝 이외의 시나리오에 유용합니다.

프로젝션의 구성 요소 [기술 저장소](knowledge-store-concept-intro.md), 기술 마이닝 목적에 대 한 물리적 저장소에 저장할 수 있는 풍부한 문서의 뷰입니다. 프로젝션을 사용 하면 데이터를 Power BI와 같은 도구에서 추가 작업 없이 데이터를 읽을 수 있도록 관계를 유지 하는 요구에 맞추는 셰이프로 "프로젝트" 수 있습니다. 

프로젝션은 Azure Table storage의 행과 열에 저장 된 데이터 또는 Azure Blob storage에 저장 된 JSON 개체를 사용 하 여 테이블 형식 수 있습니다. 보강 되는 대로 데이터의 여러 예측을 정의할 수 있습니다. 개별 사용 사례에 대 한 다른 방식으로 셰이핑 동일한 데이터를 원하는 경우에 유용 합니다. 

기술 저장소는 두 가지 유형의 예측을 지원합니다.

+ **테이블**: 가장 행과 열으로 표시 되는 데이터에 대 한 테이블 프로젝션을 사용 하면 테이블 저장소에서 스키마 화 된 도형 또는 프로젝션을 정의할 수 있습니다. 

+ **개체**: 데이터 및 강화의 JSON 표현을 경우 개체 프로젝션 blob으로 저장 됩니다.

단계별로 컨텍스트에 정의 된 예측을 확인 하려면 [기술 저장소를 사용 하 여 시작 하는 방법](knowledge-store-howto.md)

## <a name="projection-groups"></a>프로젝션 그룹

경우에 따라 다른 목표에 맞게 다른 모양에서 풍부한 데이터 프로젝트 해야 합니다. 정보 저장소를 사용 하면 예측의 여러 그룹을 정의할 수 있습니다. 프로젝션 그룹 상호 배타 성 및 관련성의 다음 주요 특징을 갖습니다.

### <a name="mutually-exclusivity"></a>독점 성을 함께 사용할 수 없습니다

단일 그룹으로 프로젝션 하는 모든 콘텐츠가 다른 프로젝션 그룹으로 프로젝션 하는 데이터와 무관 합니다. 이 동일한 데이터를 다르게 모양 아직 프로젝션 그룹별에서 반복 수 있다는 것을 의미 합니다. 

프로젝션 그룹에 적용 하는 하나의 제약 조건만 프로젝션 형식 프로젝션 그룹과 상호 배타적입니다. 테이블 프로젝션 또는 단일 그룹 내의 개체 프로젝션만 정의할 수 있습니다. 테이블 및 개체를 하려는 경우 테이블에 대해 하나의 프로젝션 그룹 및 개체에 대 한 두 번째 프로젝션 그룹을 정의 합니다.

### <a name="relatedness"></a>관련성

단일 프로젝션 그룹 내에서 예상 하는 모든 콘텐츠 데이터 내에서 관계를 유지 합니다. 생성 된 키를 기반으로 하는 관계 하 고 각 자식 노드에 부모 노드에 대 한 참조를 유지 합니다. 관계 프로젝션 그룹에 걸쳐 있지 않은 있고 테이블 또는 프로젝션 그룹 중 하나에서 생성 된 개체 다른 프로젝션 그룹의 생성 된 데이터에 관계가 없습니다.

## <a name="input-shaping"></a>셰이핑 입력
키 유효를 사용 하 여 올바른 모양 또는 구조체에서 데이터를 가져오는 것은, 테이블 또는 개체 수입니다. 주요 기능이 노출 셰이프 또는 액세스 하 고 사용 하려는 방법에 따라 데이터를 구성 하는 기능은 합니다 **쉐이 퍼** 기술 내 기술 합니다.  

프로젝션은 보강 트리에서 프로젝션의 스키마와 일치 하는 경우이 개체를 정의 하기 쉽습니다. 업데이트 된 [쉐이 퍼 기술](cognitive-search-skill-shaper.md) 보강 트리의 다른 노드에서 개체를 작성 하 고 새 노드 아래에 있는 해당 부모 수 있습니다. 합니다 **쉐이 퍼** 스킬을 사용 하면 중첩 된 개체를 사용 하 여 복합 형식을 정의할 수 있습니다.

프로젝션 해야 하는 모든 요소가 포함 된 새 도형을 정의 된 경우에 프로젝션에 대 한 원본으로 또는 다른 기술에 대 한 입력으로 이제이 셰이프를 사용할 수 있습니다.

## <a name="table-projections"></a>테이블 프로젝션

쉽게 가져오기 되므로 Power BI를 사용 하 여 데이터 탐색을 위해 테이블 프로젝션을 좋습니다. 또한 테이블 프로젝션 테이블 관계 간에 카디널리티 변경에 대 한 허용 합니다. 

프로젝션 할 수 있습니다 단일 문서 인덱스의 여러 테이블로 관계를 유지 합니다. 여러 테이블에 프로젝션 하는 경우 전체 모양은 자식 노드를 동일한 그룹 내 다른 테이블의 원본인 경우가 아니면 각 테이블에 프로젝션 됩니다.

### <a name="defining-a-table-projection"></a>테이블 프로젝션 정의

내 테이블 프로젝션을 정의 하는 경우는 `knowledgeStore` 요소 기술력을의 테이블 원본으로 보강 트리에서 노드를 매핑하여 시작 합니다. 일반적으로이 노드는 출력을 **쉐이 퍼** 테이블로 프로젝트 해야 하는 특정 도형을 생성 하는 기술 목록에 추가 하는 기술입니다. 프로젝트 하려는 노드 수 있으며 여러 테이블에는 프로젝트입니다. 테이블 정의 프로젝션 하려는 테이블의 목록입니다. 

각 테이블에는 세 가지 속성이 필요합니다.

+ tableName: Azure Storage의 테이블의 이름입니다.

+ generatedKeyName: 이 행을 고유 하 게 식별 하는 키 열 이름입니다.

+ 원본: 보강 트리에서 노드에 강화에서 소싱 됩니다. 일반적으로 쉐이 퍼의 출력 이지만 기술을의 출력 일 수 있습니다.

테이블 프로젝션의 예제는 다음과 같습니다.

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
이 예제에서 볼 수 있듯이, 핵심 문구 및 엔터티를 다른 테이블로 모델링 됩니다 하 고 각 행에 대 한 부모 (MainTable) 다시에 대 한 참조를 포함 됩니다. 

다음 그림은의 Caselaw 연습에 대 한 참조가 [지식 저장소를 사용 하 여 시작 하는 방법을](knowledge-store-howto.md)합니다. 각 의견에 포함 된 엔터티를 식별 하 여 보강 됩니다 있고 경우에 여러 의견이 있는 경우 다음과 같이 하는 예측을 모델링할 수 있습니다.

![테이블의 엔터티 및 관계로](media/knowledge-store-projection-overview/TableRelationships.png "테이블 프로젝션의 관계 모델링")

## <a name="object-projections"></a>개체 프로젝션

개체 프로젝션은 보강 트리 노드에서 제공 될 수 있는 JSON 표현입니다. 대부분의 경우 동일한 **쉐이 퍼** 테이블을 예측 하는 기술을 개체 프로젝션으로 생성 데 사용할 수 있습니다. 

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

생성 하는 개체 프로젝션에 몇 가지 개체 관련 특성을 필요 합니다.

+ storageContainer: 개체를 저장할 컨테이너
+ 원본: 프로젝션의 루트인 보강 트리 노드까지의 경로
+ 키: 저장할 개체에 대 한 고유 키를 나타내는 경로입니다. 컨테이너에 blob의 이름을 만드는 데 사용할 됩니다.

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

예상 수명 주기 데이터 원본에서 원본 데이터에 연결 된 경우 데이터 업데이트 되 고 다시 인덱싱, 예상 결과를 예상 데이터 원본에서 데이터를 사용 하 여 결과적으로 일관성이 보장 강화를 사용 하 여 업데이트 됩니다. 도법은 인덱스에 대해 구성한 삭제 정책을 상속 합니다. 

## <a name="using-projections"></a>프로젝션을 사용 하 여

인덱서를 실행 한 후에 프로젝션을 통해 지정 된 컨테이너의 예상된 데이터를 읽을 수 있습니다. 

분석을 위한 Power BI에서 탐색은 데이터 원본으로 Azure Table storage를 설정 하는 것으로 간단 합니다. 내에서 관계를 활용 하 여 데이터에 시각화 집합을 매우 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인의 풍부한 데이터를 사용 해야 할 경우 있습니다 [blob에서 데이터를 Pandas 데이터 프레임을 로드](../machine-learning/team-data-science-process/explore-data-blob.md)합니다.

마지막으로, 정보 저장소에서 데이터를 내보냅니다 해야 할 경우 Azure Data Factory에 데이터를 내보내고 사용자가 선택한 데이터베이스에 배치 하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계에서는 샘플 데이터 및 지침을 사용 하 여 첫 번째 지식 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [정보 저장소를 만드는 방법](knowledge-store-howto.md)합니다.