---
title: 기술 자료 저장소 소개 (미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 및 다른 응용 프로그램에서 보강 문서를 보고, 변형 하 고, 사용할 수 있는 Azure storage로 보강 문서를 보냅니다. 이 기능은 공개 미리 보기 상태입니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a1c6f2d869d8d7ad865005ebd319beac56bdbacd
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720085"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure Cognitive Search의 기술 자료 저장소 소개

> [!IMPORTANT] 
> 기술 자료 저장소는 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-미리](search-api-preview.md) 보기는 미리 보기 기능을 제공 합니다. 현재는 포털 지원이 제한적 이며 .NET SDK를 지원 하지 않습니다.

지식 스토어는 나중에 분석 하거나 다른 다운스트림 처리를 위해 [AI 보강 파이프라인](cognitive-search-concept-intro.md) 의 출력을 유지 하는 Azure Cognitive Search의 기능입니다. *보강 문서* 는 AI 프로세스를 사용 하 여 추출, 구조화 및 분석 된 콘텐츠에서 만들어진 파이프라인의 출력입니다. 표준 AI 파이프라인에서 보강 문서는 일시적 이며 인덱싱 중에만 사용 된 후 삭제 됩니다. 지식 스토어를 사용 하 여 보강 문서는 유지 됩니다. 

과거에 Azure Cognitive Search로 인식 기술을 사용 하는 경우 강화 시퀀스를 통해 문서를 이동 하는 것을 이미 알고 *기술력과* . 결과는 기술 자료 저장소에서 검색 인덱스 또는 (이 미리 보기의 새) 프로젝션을 될 수 있습니다. 검색 인덱스와 기술 자료 저장소 라는 두 출력은 동일한 콘텐츠를 공유 하지만 매우 다양 한 방식으로 저장 및 사용 됩니다.

물리적으로 기술 자료 저장소는 Azure Table storage, Azure Blob storage 중 하나 또는 둘 모두 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview)됩니다. Azure Storage에 연결할 수 있는 모든 도구 또는 프로세스는 지식 저장소의 콘텐츠를 사용할 수 있습니다.

![파이프라인 다이어그램의 기술 자료 저장소](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "파이프라인 다이어그램의 기술 자료 저장소")

지식 저장소를 사용하려면 인덱싱 파이프라인의 단계별 작업을 정의하는 기술 세트에 `knowledgeStore` 요소를 추가합니다. Azure Cognitive Search는 실행 하는 동안 Azure storage 계정에 공간을 만들고 구성에 따라 보강 문서를 blob 또는 테이블로 프로젝션 합니다.

## <a name="benefits-of-knowledge-store"></a>지식 저장소의 이점

지식 저장소는 BLOB과 같은 비정형 및 준정형 데이터 파일, 분석을 실행한 이미지 파일 또는 새 양식으로 변형된 정형 데이터에서 수집한 구조, 상황 및 실제 콘텐츠를 제공합니다. 단계별 [연습](knowledge-store-howto.md)에서는 조밀한 JSON 문서를 하위 구조체로 분할 하는 방법, 다시 구성를 새 구조체로 분할 하는 방법, 그리고 기계 학습 및 데이터 과학 같은 다운스트림 프로세스에 사용할 수 있는 방법을 확인할 수 있습니다. dss.

AI 보강 파이프라인이 생성할 수 있는 기능을 확인 하는 것이 유용 하지만 기술 자료 저장소의 실제 기능은 데이터를 변경할 수 있는 기능입니다. 기본 기술 시작 하 고이를 반복 하 여 더 많은 구조 수준을 추가할 수 있습니다. 그런 다음 Azure Cognitive Search 이외에 다른 앱에서 사용할 수 있는 새 구조로 결합할 수 있습니다.

지식 저장소의 이점을 자세히 열거하면 다음과 같습니다.

+ 보강된 문서를 검색 이외의 [분석 및 보고 도구](#tools-and-apps)에 사용합니다. 파워 쿼리 포함 Power BI는 매력적인 선택이지만 Azure Storage에 연결할 수 있는 모든 도구 또는 앱은 사용자가 만드는 지식 저장소에서 끌어올 수 있습니다.

+ 단계 및 기술 세트 정의를 디버깅하는 동안 AI 인덱싱 파이프라인을 정교화합니다. 지식 저장소는 AI 인덱싱 파이프라인에 있는 기술 세트 정의의 생성물을 보여줍니다. 보강이 어떻게 보이는지 정확히 확인할 수 있으므로 해당 결과를 사용하여 더 나은 기술 세트를 디자인할 수 있습니다. Azure Storage의 [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)를 사용하여 지식 저장소의 콘텐츠를 볼 수 있습니다.

+ 데이터를 새 양식으로 변형합니다. 변형은 기술 세트에 포함되지만 중요한 점은 이제 기술 세트가 이 기능을 제공할 수 있다는 것입니다. Azure Cognitive Search의 [Shaper 기술이](cognitive-search-skill-shaper.md) 이 작업을 수용할 수 있도록 확장 되었습니다. 변형을 사용하면 관계를 유지하면서 데이터의 사용 목적에 맞는 프로젝션을 정의할 수 있습니다.

> [!Note]
> AI 보강 및 인식 기술을 처음 접하는 가요? Azure Cognitive Search는 Cognitive Services 비전 및 언어 기능과 통합 되어 이미지 파일에 대해 OCR (광학 문자 인식)을 사용 하 여 원본 데이터를 추출 하 고 보강 하며, 텍스트 파일에서 엔터티 인식 및 키 문구 추출 등을 사용 합니다. 자세한 내용은 [Azure Cognitive Search의 AI 보강](cognitive-search-concept-intro.md)를 참조 하세요.

## <a name="creating-a-knowledge-store"></a>기술 자료 저장소 만들기

기술 자료 저장소는 [기술](cognitive-search-working-with-skillsets.md)의 일부 이며,이는 [인덱서의](search-indexer-overview.md)일부입니다. 

이 미리 보기에서는 REST API 및 `api-version=2019-05-06-Preview`사용 하거나 포털에서 **데이터 가져오기** 마법사를 사용 하 여 기술 자료 저장소를 만들 수 있습니다.

### <a name="json-representation-of-a-knowledge-store"></a>기술 자료 저장소의 JSON 표현

다음 JSON은 인덱서(표시되지 않음)가 호출하는 기술 세트의 일부인 `knowledgeStore`를 지정합니다. AI 보강에 대해 이미 잘 알고 있는 경우 기술는 각 보강 문서의 생성, 구성 및 관리를 결정 합니다. 기술에는 하나 이상의 기술이 포함 되어야 하며, 이러한 데이터 구조를 사용할 경우 Shaper 기술이 있을 수 있습니다.

`knowledgeStore`는 연결과 프로젝션으로 구성 됩니다. 

+ Azure Cognitive Search와 동일한 지역에 있는 저장소 계정에 연결 합니다. 

+ 프로젝션은 테이블-개체 쌍입니다. Azure 테이블 저장소에서 보강 문서의 물리적 식을 정의 합니다. `Tables` Azure Blob storage에서 물리적 개체를 정의 `Objects` 합니다.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
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

### <a name="sources-of-data-for-a-knowledge-store"></a>기술 자료 저장소에 대 한 데이터 원본

지식 저장소가 AI 보강 파이프라인에서 출력 되는 경우 입력은 무엇 인가요? 추출, 보강 및 궁극적으로 기술 자료 저장소에 저장 하려는 원본 데이터는 검색 인덱서에서 지 원하는 모든 Azure 데이터 원본에서 가져올 수 있습니다. 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table Storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

인덱서 및 기술력과는 인덱싱 워크 로드의 일부로이 콘텐츠를 추출 하 고 보강 하거나 변형 한 다음 결과를 기술 자료 저장소에 저장 합니다.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>지식 저장소를 만들 때 사용 되는 REST Api

기술 자료 저장소 (Create 기술 및 Create 인덱서)를 만드는 데 필요한 확장은 두 개의 Api에만 포함 됩니다. 다른 Api는 있는 그대로 사용 됩니다.

| Object | REST API | 설명 |
|--------|----------|-------------|
| 데이터 원본 | [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 보강된 문서를 만드는 데 사용되는 원본 데이터를 제공하는 외부 Azure 데이터 원본을 식별하는 리소스입니다.  |
| 기술 세트 | [기술 세트 만들기(api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 인덱싱 중에 보강 파이프라인에 사용되는 [기본 제공 기술](cognitive-search-predefined-skills.md) 및 [사용자 지정 인식 기술](cognitive-search-custom-skill-interface.md)의 사용을 조정하는 리소스입니다. 기술에는 자식 요소로 `knowledgeStore` 정의가 있습니다. |
| index | [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)  | 검색 인덱스를 표현 하는 스키마입니다. 원본 데이터 또는 보강 단계에서 생성되는 필드에 매핑되는 인덱스의 필드(예: 엔터티 인식으로 생성된 조직 이름에 대한 필드)입니다. |
| 인덱서 | [인덱서 만들기(api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 데이터 원본, 기술 집합, 원본 및 중간 데이터 구조에서 대상 인덱스로 필드 연결 및 인덱스 자체를 포함하는 인덱싱 중에 사용되는 리소스를 정의하는 구성 요소입니다. 데이터 수집 및 보강을 위한 트리거가 인덱서를 실행합니다. 출력은 기술 세트를 통해 보강된 원본 데이터로 채워진 인덱스 스키마를 기반으로 하는 검색 인덱스입니다.  |

### <a name="physical-composition-of-a-knowledge-store"></a>지식 저장소의 물리적 컴퍼지션

 `knowledgeStore` 정의의 요소인 *프로젝션*은 의도 된 용도와 일치 하도록 출력의 스키마와 구조를 명확히 합니다. 서로 다른 형식 및 모양으로 데이터를 사용하는 애플리케이션이 있는 경우 여러 프로젝션을 정의할 수 있습니다. 

프로젝션은 개체 또는 테이블로서 다음과 같이 명시될 수 있습니다.

+ 개체인 프로젝션은 BLOB 스토리지에 매핑되며 프로젝션은 컨테이너에 저장됩니다. 컨테이너 내에는 데이터 과학 파이프라인과 같은 시나리오에 대한 JSON의 개체 또는 계층 표시가 있습니다.

+ 테이블인 프로젝션은 테이블 스토리지에 매핑됩니다. 테이블 표시는 데이터 분석 또는 기계 학습용 데이터 프레임으로 내보내기와 같은 시나리오에 대한 관계를 유지합니다. 그런 다음, 보강된 프로젝션을 다른 데이터 원본으로 쉽게 가져올 수 있습니다. 

지식 저장소에 조직의 다양한 사용자에 맞는 여러 개의 프로젝션을 만들 수 있습니다. 개발자는 보강된 문서의 전체 JSON 표시에 액세스해야 할 수 있는 반면, 데이터 과학자 또는 분석자는 기술 세트에 의해 형성된 세분화된 또는 모듈형 데이터 구조를 원할 수 있습니다.

예를 들어 보강 프로세스의 목표 중 하나가 모델을 학습하는 데 사용되는 데이터 세트를 만드는 것이기도 한 경우 데이터를 개체 저장소에 프로젝션하는 것은 데이터 과학 파이프라인의 데이터를 사용하는 한 가지 방법이 될 수 있습니다. 또는 보강된 문서를 기반으로 빠른 Power BI 대시보드를 만들려는 경우 테이블 형식의 프로젝션이 적합할 수 있습니다.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>도구 및 앱과 연결

보강이 스토리지에 존재하면 Azure BLOB 또는 테이블 스토리지에 연결하는 도구 또는 기술을 사용하여 콘텐츠를 검색, 분석 또는 사용할 수 있습니다. 다음 목록으로 시작합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) - 보강된 문서 구조 및 콘텐츠를 봅니다. 이 도구를 지식 저장소 콘텐츠를 보기 위한 기준선 도구로 고려합니다.

+ 숫자 데이터가 있는 경우 보고 및 분석 도구에 대 한 [Power BI](knowledge-store-connect-power-bi.md) 합니다.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) - 추가 조작을 위한 도구입니다.

## <a name="next-steps"></a>다음 단계

기술 자료 저장소는 보강 문서를 지 원하는 기능을 제공 하며, 기술를 디자인할 때 유용 하며, Azure Storage 계정에 액세스할 수 있는 클라이언트 응용 프로그램에서 사용할 수 있도록 새 구조와 콘텐츠를 만드는 경우에 유용 합니다.

보강 문서를 만드는 가장 간단한 방법은 **데이터 가져오기** 마법사를 사용 하는 것 이지만, 개체를 만들고 참조 하는 방법에 대 한 통찰력을 원하는 경우에는 postman 및 REST API를 사용할 수도 있습니다.

> [!div class="nextstepaction"]
> [포털을 사용 하 여 기술 자료 저장소 만들기](knowledge-store-create-portal.md)
> [POSTMAN 및 REST APi를 사용 하 여 기술 자료 저장소 만들기](knowledge-store-create-rest.md)
