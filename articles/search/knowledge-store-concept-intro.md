---
title: 지식 저장소 소개(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 및 기타 응용 프로그램에서 보강된 문서를 보고, 모양을 변경하고, 사용할 수 있는 Azure 저장소로 보강된 문서를 보냅니다. 이 기능은 공개 미리 보기 상태입니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942989"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소 소개

> [!IMPORTANT] 
> 지식 저장소는 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-Preview](search-api-preview.md)는 미리 보기 기능을 제공합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

지식 저장소는 독립적인 분석 또는 다운스트림 처리를 위해 [AI 보강 파이프라인의](cognitive-search-concept-intro.md) 출력을 유지하는 Azure Cognitive Search의 기능입니다. *보강된 문서*는 AI 프로세스를 사용하여 추출, 구조화 및 분석된 콘텐츠에서 만든 파이프라인의 출력입니다. 표준 AI 파이프라인에서 보강된 문서는 일시적이며 인덱싱 중에만 사용된 후에 삭제됩니다. 보강된 문서는 지식 저장소를 사용하여 유지됩니다. 

과거에 인지 능력을 사용했다면 *기술 집합이* 일련의 보강을 통해 문서를 이동한다는 것을 이미 알고 있습니다. 결과는 검색 인덱스이거나 지식 저장소의 프로젝션(이 미리 보기의 새로운 기능)일 수 있습니다. 검색 인덱스와 지식 저장소의 두 출력은 동일한 파이프라인의 제품입니다. 동일한 입력에서 파생되지만 구조화, 저장 및 매우 다른 방식으로 사용되는 출력이 생성됩니다.

물리적으로 지식 저장소는 Azure Table 스토리지, Azure Blob 스토리지 또는 둘 다인 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview)입니다. Azure Storage에 연결할 수 있는 모든 도구 또는 프로세스는 지식 저장소의 콘텐츠를 사용할 수 있습니다.

![파이프라인의 지식 저장소 다이어그램](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "파이프라인의 지식 저장소 다이어그램")

## <a name="benefits-of-knowledge-store"></a>지식 저장소의 이점

지식 저장소는 Blob, 분석을 거친 이미지 파일 또는 구조화 된 데이터와 같은 구조화되지 않은 반 구조화 된 데이터 파일에서 수집 된 구조, 컨텍스트 및 실제 콘텐츠를 새로운 형태로 재구성합니다. [단계별 연습에서는](knowledge-store-create-rest.md)조밀한 JSON 문서가 어떻게 하위 구조로 분할되고, 새로운 구조로 재구성되는지, 그렇지 않으면 기계 학습 및 데이터 과학 워크로드와 같은 다운스트림 프로세스에 사용할 수 있는지 직접 확인할 수 있습니다.

AI 보강 파이프라인이 생성할 수 있는 것을 확인하는 것이 유용하지만 지식 저장소의 실제 잠재력은 데이터를 재구성하는 기능입니다. 기본 기술 세트로 시작하고, 이를 반복하여 증가하는 수준의 구조를 추가한 다음, Azure Cognitive Search 이외의 다른 앱에서 사용할 수 있는 새 구조로 결합할 수 있습니다.

지식 저장소의 이점을 자세히 열거하면 다음과 같습니다.

+ 보강된 문서를 검색 이외의 [분석 및 보고 도구](#tools-and-apps)에 사용합니다. Power Query를 사용하여 Power BI를 선택하는 것은 강력한 선택이지만 Azure Storage에 연결할 수 있는 모든 도구 또는 앱은 만든 기술 저장소에서 가져올 수 있습니다.

+ 단계 및 기술 세트 정의를 디버깅하는 동안 AI 인덱싱 파이프라인을 정교화합니다. 지식 저장소는 AI 인덱싱 파이프라인에 있는 기술 세트 정의의 생성물을 보여줍니다. 보강이 어떻게 보이는지 정확히 확인할 수 있으므로 해당 결과를 사용하여 더 나은 기술 세트를 디자인할 수 있습니다. Azure 저장소의 [저장소 탐색기를](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) 사용하여 기술 저장소의 내용을 볼 수 있습니다.

+ 데이터를 새 양식으로 변형합니다. 변형은 기술 세트에 포함되지만 중요한 점은 이제 기술 세트가 이 기능을 제공할 수 있다는 것입니다. Azure Cognitive Search의 [쉐이퍼 기술](cognitive-search-skill-shaper.md)은 이 작업에 맞게 확장되었습니다. 변형을 사용하면 관계를 유지하면서 데이터의 사용 목적에 맞는 프로젝션을 정의할 수 있습니다.

> [!Note]
> AI 의 풍요로움과 인지 능력이 새로운가요? Azure Cognitive Search는 Cognitive Services 비전 및 언어 기능과 통합되어 이미지 파일, 엔터티 인식 및 텍스트 파일에서 핵심 구 추출 등에 OCR(광학 문자 인식)을 사용하여 원본 데이터를 추출하고 보강합니다. 자세한 내용은 [Azure Cognitive Search의 AI 보강](cognitive-search-concept-intro.md)을 참조하세요.

## <a name="physical-storage"></a>물리적 스토리지

기술 저장소의 물리적 표현은 기술 집합의 `projections` `knowledgeStore` 정의 요소를 통해 명확하게 표현됩니다. 프로젝션은 의도한 용도와 일치되도록 출력 구조를 정의합니다.

투영은 테이블, 객체 또는 파일로 관절될 수 있습니다.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

이 구조에서 지정한 투영 유형에 따라 지식 저장소에서 사용하는 저장소 유형이 결정됩니다.

+ 테이블 저장소는 을 `tables`정의할 때 사용됩니다. 해석 도구에 대한 입력을 위한 테이블 형식 보고 구조가 필요하거나 다른 데이터 저장소에 데이터 프레임으로 내보낼 때 테이블 투영을 정의합니다. 보강된 문서의 하위 집합 또는 횡단면을 얻으려면 여러 를 `tables` 지정할 수 있습니다. 동일한 프로젝션 그룹 내에서 테이블 관계는 모두 사용할 수 있도록 유지됩니다.

+ Blob 저장소는 정의하거나 `objects` `files`. a의 `object` 물리적 표현은 보강된 문서를 나타내는 계층적 JSON 구조입니다. A는 `file` 문서에서 추출한 이미지로, Blob 저장소로 그대로 전송됩니다.

단일 프로젝션 개체에는 `tables` `objects`하나의 `files`집합이 포함되어 있으며 많은 시나리오에서 하나의 프로젝션을 만드는 것으로 충분할 수 있습니다. 

그러나 여러 `table` - `object` - `file` 투영 집합을 만들 수 있으며 다른 데이터 관계를 원하는 경우 그렇게 할 수 있습니다. 집합 내에서 데이터가 관련되어 있으며 이러한 관계가 존재하고 검색할 수 있다고 가정합니다. 추가 집합을 만드는 경우 각 그룹의 문서는 서로 관련되지 않습니다. 여러 프로젝션 그룹을 사용하는 예로는 온라인 시스템과 함께 사용하기 위해 동일한 데이터를 프로젝션하고 특정 방식으로 표현해야 하는 경우 와 같은 데이터를 표현되는 데이터 과학 파이프라인에 사용하기 위해 투영된 데이터도 포함될 수 있습니다. 다르게.

## <a name="requirements"></a>요구 사항 

[Azure 저장소가](https://docs.microsoft.com/azure/storage/) 필요합니다. 그것은 물리적 스토리지를 제공합니다. Blob 저장소, 테이블 저장소 또는 둘 다 사용할 수 있습니다. Blob 저장소는 일반적으로 출력이 다운스트림 프로세스로 이동할 때 손상되지 않은 보강된 문서에 사용됩니다. 테이블 저장소는 분석 및 보고에 일반적으로 사용되는 보강된 문서 조각을 위한 것입니다.

[기술 집합이](cognitive-search-working-with-skillsets.md) 필요합니다. 여기에는 `knowledgeStore` 정의가 포함되어 있으며 보강된 문서의 구조와 구성을 결정합니다. 빈 기술 집합을 사용하여 기술 저장소를 만들 수 없습니다. 기술 집합에 하나 이상의 기술이 있어야 합니다.

[인덱서가](search-indexer-overview.md) 필요합니다. 기술 집합은 실행을 구동하는 인덱서에 의해 호출됩니다. 인덱서에는 고유한 요구 사항 및 특성 집합이 있습니다. 이러한 특성 중 일부는 지식 저장소에 직접적인 영향을 미를 갖습니다.

+ 인덱서에는 [지원되는 Azure 데이터 원본(궁극적으로](search-indexer-overview.md#supported-data-sources) 기술 저장소를 만드는 파이프라인은 Azure에서 지원되는 원본에서 데이터를 가져오는 것으로 시작됩니다)가 필요합니다. 

+ 인덱서에는 검색 인덱스가 필요합니다. 인덱서에서는 인덱스 스키마를 사용할 계획이 없더라도 인덱스 스키마를 제공해야 합니다. 최소 인덱스에는 키로 지정된 하나의 문자열 필드가 있습니다.

+ 인덱서에서는 소스 필드를 대상 필드에 별칭으로 하는 데 사용되는 선택적 필드 매핑을 제공합니다. 기본 필드 매핑에서 다른 이름이나 형식을 사용하려면 수정이 필요한 경우 인덱서 내에서 [필드 매핑을](search-indexer-field-mappings.md) 만들 수 있습니다. 지식 저장소 출력의 경우 대상은 Blob 개체 또는 테이블의 필드일 수 있습니다.

+ 인덱서에는 일정이 있으며 다양한 데이터 원본에서 제공하는 변경 검색 메커니즘과 같은 기타 속성도 지식 저장소에 적용할 수 있습니다. 예를 들어 정기적으로 보강을 [예약하여](search-howto-schedule-indexers.md) 내용을 새로 고칠 수 있습니다. 

## <a name="how-to-create-a-knowledge-store"></a>지식 저장소를 만드는 방법

지식 저장소를 만들려면 포털 또는 REST`api-version=2019-05-06-Preview`API(미리 보기)를 사용합니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

**데이터 가져오기** 마법사에는 기술 저장소를 만드는 옵션이 포함되어 있습니다. 초기 탐색의 경우 [네 단계로 첫 번째 지식 저장소를 만듭니다.](knowledge-store-connect-power-bi.md)

1. 지원되는 데이터 원본을 선택합니다.

1. 보강 지정: 리소스를 연결하고 기술을 선택하고 지식 저장소를 지정합니다. 

1. 인덱스 스키마를 만듭니다. 마법사가 필요하며 이를 유추할 수 있습니다.

1. 마법사를 실행합니다. 이 마지막 단계에서 추출, 농축 및 저장이 발생합니다.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>기술 집합 만들기 및 REST 미리 보기 API 사용

A는 `knowledgeStore` 기술 [집합](cognitive-search-working-with-skillsets.md)내에서 정의되며 [인덱서에](search-indexer-overview.md)의해 호출됩니다. 보강하는 동안 Azure Cognitive Search는 Azure Storage 계정에 공간을 만들고 구성에 따라 보강된 문서를 Blob 또는 테이블로 프로젝트합니다.

현재 미리 보기 REST API는 프로그래밍 방식으로 지식 저장소를 만들 수 있는 유일한 메커니즘입니다. 쉽게 탐색할 수 있는 방법은 [Postman 및 REST API를 사용하여 첫 번째 지식 저장소를 만드는 것입니다.](knowledge-store-create-rest.md)

이 미리 보기 기능에 대한 참조 콘텐츠는 이 문서의 [API 참조](#kstore-rest-api) 섹션에 있습니다. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>도구 및 앱과 연결하는 방법

보강이 스토리지에 존재하면 Azure BLOB 또는 테이블 스토리지에 연결하는 도구 또는 기술을 사용하여 콘텐츠를 검색, 분석 또는 사용할 수 있습니다. 다음 목록으로 시작합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) - 보강된 문서 구조 및 콘텐츠를 봅니다. 이 도구를 지식 저장소 콘텐츠를 보기 위한 기준선 도구로 고려합니다.

+ 보고 및 분석을 위한 [전력 BI.](knowledge-store-connect-power-bi.md) 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) - 추가 조작을 위한 도구입니다.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 참조

REST API `2019-05-06-Preview` 버전은 기술 집합에 대한 추가 정의를 통해 지식 저장소를 제공합니다. 참조 외에도 [Postman을 사용하여 기술 저장소 만들기를 참조하여](knowledge-store-create-rest.md) API를 호출하는 방법에 대한 자세한 내용을 참조하세요.

+ [기술 세트 만들기(api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [기술 집합 업데이트(api 버전=2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>다음 단계

지식 저장소는 보강된 문서를 지속적으로 유지하므로 기술 세트를 디자인하거나 Azure Storage 계정에 액세스할 수 있는 모든 클라이언트 애플리케이션에서 사용할 새 구조와 콘텐츠를 만들 때 유용합니다.

보강된 문서를 만드는 가장 간단한 방법은 [포털을 사용하는](knowledge-store-create-portal.md)것이지만 Postman 및 REST API를 사용할 수도 있습니다.

> [!div class="nextstepaction"]
> [우체부 및 REST를 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)

프로젝션, 기능 및 [기술 집합에서](knowledge-store-projection-overview.md) 프로젝션을 정의하는 방법에 대해 자세히 알아보려면

> [!div class="nextstepaction"]
> [지식 저장소의 프로젝션](knowledge-store-projection-overview.md)

슬라이싱, 인라인 형성 및 관계와 같은 고급 프로젝션 개념을 다루는 자습서의 경우 [지식 저장소에서 프로젝션 정의로](knowledge-store-projections-examples.md) 시작하십시오.

> [!div class="nextstepaction"]
> [지식 저장소에서 투영 정의](knowledge-store-projections-examples.md)