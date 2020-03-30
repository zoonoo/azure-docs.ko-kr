---
title: Azure 포털을 사용하여 검색 인덱스로 데이터 가져오기
titleSuffix: Azure Cognitive Search
description: Azure Portal에서 데이터 가져오기 마법사를 사용하여 Azure VM에 있는 Cosmos DB, Blob Storage, 테이블 스토리지, SQL Database 및 SQL Server에서 Azure 데이터를 크롤링하는 방법을 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460706"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Azure 인지 검색에 대 한 데이터 마법사 가져오기

Azure 포털은 인덱스를 프로토타이핑하고 로드하기 위해 Azure Cognitive Search 대시보드에서 **데이터 가져오기** 마법사를 제공합니다. 이 문서에서는 마법사 사용의 장점과 제한 사항, 입력 및 출력 및 일부 사용 정보를 다룹니다. 기본 제공 샘플 데이터를 사용하여 마법사를 단계별 실습 지침은 Azure portal 빠른 시작을 [사용하여 Azure 인지 검색 인덱스 만들기를](search-get-started-portal.md) 참조하세요.

이 마법사가 수행하는 작업은 다음과 같습니다.

1 - 지원되는 Azure 데이터 원본에 연결합니다.

2 - 원본 데이터를 샘플링하여 유추된 인덱스 스키마를 만듭니다.

3 - 선택적으로 AI 보강을 추가하여 콘텐츠와 구조를 추출하거나 생성합니다.

4 - 마법사를 실행하여 개체를 만들고, 데이터를 가져오고, 일정 및 기타 구성 옵션을 설정합니다.

마법사는 검색 서비스에 저장된 여러 개체를 출력하며, 이 개체는 프로그래밍 방식으로 또는 다른 도구에서 액세스할 수 있습니다.

## <a name="advantages-and-limitations"></a>장점 및 제한 사항

코드를 작성하기 전에 마법사를 사용하여 프로토타이핑 및 개념 증명 테스트를 수행할 수 있습니다. 마법사는 외부 데이터 원본에 연결하고 데이터를 샘플링하여 초기 인덱스를 만든 다음 JSON 문서로 데이터를 Azure Cognitive Search의 인덱스로 가져옵니다. 

샘플링은 인덱스 스키마가 유추되는 프로세스이며 몇 가지 제한사항이 있습니다. 데이터 원본을 만들 때 마법사는 문서 샘플을 선택하여 데이터 원본의 일부인 열을 결정합니다. 매우 큰 데이터 원본에 대해 몇 시간이 걸릴 수 있기 때문에 모든 파일을 읽는 것은 아닙니다. 선택된 문서가 주어지면 필드 이름이나 형식과 같은 소스 메타데이터가 인덱스 스키마에서 필드 컬렉션을 만드는 데 사용됩니다. 원본 데이터의 복잡성에 따라 정확성을 위해 초기 스키마를 편집하거나 완전성을 위해 확장해야 할 수 있습니다. 인덱스 정의 페이지에서 변경 내용을 인라인으로 만들 수 있습니다.

전반적으로 마법사를 사용할 때의 장점은 요구 사항이 충족되는 한 몇 분 내에 쿼리 가능한 인덱스를 프로토타입으로 만들 수 있다는 점입니다. JSON 문서와 같은 데이터 제공과 같은 인덱싱의 복잡성 중 일부는 마법사에서 처리합니다.

알려진 제한 사항은 다음과 같이 요약됩니다.

+ 마법사는 반복 또는 재사용을 지원하지 않습니다. 마법사를 통과할 때마다 새 인덱스, 기술 집합 및 인덱서 구성이 만들어집니다. 데이터 원본만 마법사 내에서 유지 및 재사용할 수 있습니다. 다른 객체를 편집하거나 구체화하려면 REST API 또는 .NET SDK를 사용하여 구조를 검색하고 수정해야 합니다.

+ 원본 콘텐츠는 지원되는 Azure 데이터 원본에 있어야 합니다.

+ 샘플링은 원본 데이터의 하위 집합이상입니다. 큰 데이터 원본의 경우 마법사가 필드를 놓칠 수 있습니다. 샘플링이 충분하지 않은 경우 스키마를 확장하거나 유추된 데이터 형식을 수정해야 할 수 있습니다.

+ 포털에 노출된 AI 보강은 몇 가지 기본 제공 기술로 제한됩니다. 

+ 마법사에서 만들 수 있는 [기술 저장소는](knowledge-store-concept-intro.md)몇 가지 기본 프로젝션으로 제한됩니다. 마법사에서 만든 보강된 문서를 저장하려면 Blob 컨테이너 및 테이블에 기본 이름과 구조가 함께 제공됩니다.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>데이터 소스 입력

**가져오기 데이터** 마법사는 원본을 샘플링하고, 메타데이터를 읽고, 콘텐츠를 검색하고, 콘텐츠를 읽고, 콘텐츠를 읽고, 콘텐츠를 JSON으로 직렬화하여 Azure Cognitive Search에 대한 후속 가져오기를 위해 필요한 Azure Cognitive Search 인덱서에서 제공하는 내부 논리를 사용하여 외부 데이터 원본에 연결합니다.

단일 테이블, 데이터베이스 뷰 또는 이에 상응하는 데이터 구조에서만 가져올 수 있지만 구조에는 계층적 또는 중첩된 하위 구조가 포함될 수 있습니다. 자세한 내용은 [복합 형식을 모델링하는 방법을](search-howto-complex-data-types.md)참조하십시오.

마법사를 실행하기 전에 이 단일 테이블 또는 뷰를 만들어야 하며 콘텐츠가 포함되어야 합니다. 명백한 이유로 빈 데이터 원본에서 데이터 **가져오기** 마법사를 실행하는 것은 의미가 없습니다.

|  선택 | 설명 |
| ---------- | ----------- |
| **기존 데이터 원본** |검색 서비스에 이미 정의된 인덱서가 있는 경우 다시 사용할 수 있는 기존 데이터 원본 정의가 있을 수 있습니다. Azure Cognitive Search에서 데이터 원본 개체는 인덱서에서만 사용됩니다. 프로그래밍 방식으로 또는 **데이터 가져오기** 마법사를 통해 데이터 원본 개체를 만들고 필요에 따라 다시 사용할 수 있습니다.|
| **샘플**| Azure Cognitive Search는 자습서 및 빠른 시작에 사용되는 두 개의 기본 제공 샘플 데이터 원본인 부동산 SQL 데이터베이스와 Cosmos DB에서 호스팅되는 호텔 데이터베이스를 제공합니다. 호텔 샘플을 기반으로 한 워크는 Azure 포털 퀵스타트의 [인덱스 만들기를](search-get-started-portal.md) 참조하십시오. |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |읽기 권한이 포함된 데이터베이스 사용자에 대한 서비스 이름, 자격 증명 및 데이터베이스 이름은 페이지에 또는 ADO.NET 연결 문자열을 통해 지정될 수 있습니다. 연결 문자열 옵션을 선택하여 속성을 보거나 사용자 지정합니다. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다.|
| **Azure VM에서 SQL Server** |정규화된 서비스 이름, 사용자 ID와 암호 및 데이터베이스를 연결 문자열로 지정합니다. 이 데이터 원본을 사용하려면 연결을 암호화하는 로컬 저장소에 미리 인증서를 설치해야 합니다. 지침은 Azure [인지 검색에 대한 SQL VM 연결을](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)참조하십시오. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다. |
| [**Azure 코스모스 DB**](search-howto-index-cosmosdb.md)|요구 사항은 계정, 데이터베이스 및 컬렉션을 포함합니다. 컬렉션의 모든 문서는 인덱스에 포함됩니다. 행 집합을 병합하거나 필터링하거나 쿼리를 비워 두도록 쿼리를 정의할 수 있습니다. 이 마법사에서는 쿼리가 필요하지 않습니다.|
| [**Azure Blob 저장소**](search-howto-indexing-azure-blob-storage.md) |요구 사항은 스토리지 계정 및 컨테이너를 포함합니다. 필요에 따라 그룹화 목적으로 Blob 이름이 가상 명명 규칙을 따르는 경우 이름의 가상 디렉터리 부분을 컨테이너의 폴더로 지정할 수 있습니다. 자세한 내용은 [Blob Storage 인덱싱](search-howto-indexing-azure-blob-storage.md)을 참조하세요. |
| [**Azure 테이블 저장소**](search-howto-indexing-azure-tables.md) |요구 사항은 스토리지 계정 및 테이블 이름을 포함합니다. 필요에 따라 쿼리를 지정하여 테이블의 하위 집합을 검색할 수 있습니다. 자세한 내용은 [Table Storage 인덱싱](search-howto-indexing-azure-tables.md)을 참조하세요. |

## <a name="wizard-output"></a>마법사 출력

이면에 마법사는 다음 개체를 만들고 구성하고 호출합니다. 마법사가 실행된 후 포털 페이지에서 해당 출력을 찾을 수 있습니다. 서비스의 개요 페이지에는 인덱스, 인덱서, 데이터 원본 및 기술 집합 목록이 있습니다. 인덱스 정의는 포털에서 전체 JSON에서 볼 수 있습니다. 다른 정의의 경우 REST [API를](https://docs.microsoft.com/rest/api/searchservice/) 사용하여 특정 개체를 가져옵니다.

| Object | 설명 | 
|--------|-------------|
| [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 자격 증명을 포함하여 원본 데이터에 대한 연결 정보를 유지합니다. 데이터 원본 개체는 인덱서에서만 사용됩니다. | 
| [인덱스](https://docs.microsoft.com/rest/api/searchservice/create-index) | 전체 텍스트 검색 및 기타 쿼리에 사용되는 물리적 데이터 구조입니다. | 
| [기술 집합](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | 이미지 파일에서 정보 분석 및 추출을 포함하여 콘텐츠를 조작, 변환 및 형성하기 위한 전체 지침 집합입니다. 매우 간단하고 제한된 구조를 제외하고, 그것은 농축을 제공하는 인지 서비스 리소스에 대한 참조를 포함한다. 선택적으로 지식 저장소 정의 포함될 수도 있습니다.  | 
| [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | 데이터 원본, 대상 인덱스, 선택적 기술 집합, 선택적 일정 및 오류 전달 및 기본 64 인코딩을 위한 선택적 구성 설정을 지정하는 구성 개체입니다. |


## <a name="how-to-start-the-wizard"></a>마법사를 시작하는 방법

데이터 가져오기 마법사는 서비스 개요 페이지의 명령 모음에서 시작됩니다.

1. [Azure Portal](https://portal.azure.com)의 대시보드에서 검색 서비스 페이지를 열거나 서비스 목록에서 [서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. 맨 위에 있는 서비스 개요 페이지에서 **데이터 가져오기**를 클릭합니다.

   ![포털에서 데이터 명령 가져오기](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

Azure Cosmos DB, Azure SQL Database 및 Azure Blob 저장소를 비롯한 다른 Azure 서비스에서 **가져오기 데이터를** 시작할 수도 있습니다. 서비스 개요 페이지에서 왼쪽 탐색 창에서 **Azure 인지 검색 추가를** 찾습니다.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>마법사에서 인덱스 스키마를 편집하거나 완료하는 방법

마법사는 입력 데이터 원본에서 가져온 문서로 채워지는 불완전한 인덱스를 생성합니다. 함수 인덱스의 경우 다음 요소를 정의했는지 확인합니다.

1. 필드 목록이 완료되어 있습니까? 샘플링이 누락된 새 필드를 추가하고 검색 경험에 값을 추가하지 않거나 [필터 표현식](search-query-odata-filter.md) 또는 점수 [매기기 프로필에](index-add-scoring-profiles.md)사용되지 않는 필드를 제거합니다.

1. 들어오는 데이터에 적합한 데이터 형식입니까? Azure Cognitive Search는 [EDM(엔터티 데이터 모델) 데이터 형식을](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)지원합니다. Azure SQL 데이터의 경우 동등한 값을 배치하는 [매핑 차트가](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) 있습니다. 자세한 배경은 [필드 매핑 및 변환을](search-indexer-field-mappings.md)참조하십시오.

1. *키*역할을 할 수 있는 필드가 하나 있습니까? 이 필드는 Edm.string이어야 하며 문서를 고유하게 식별해야 합니다. 관계형 데이터의 경우 기본 키에 매핑될 수 있습니다. Blob의 경우 `metadata-storage-path`. 필드 값에 공백 또는 대시를 포함하는 경우 **고급 옵션**의 **인덱서 만들기** 단계에서 **Base-64 인코딩 키** 옵션을 설정하여 이러한 문자에 대한 유효성 검사를 비활성화해야 합니다.

1. 특성을 설정하여 해당 필드가 인덱스에서 사용되는 방식을 결정합니다. 

   특성이 인덱스의 필드의 실제 식을 결정하기 때문에 이 단계를 수행하십시오. 나중에 프로그래밍 방식으로도 특성을 변경하려면 거의 항상 인덱스를 삭제하고 다시 빌드해야 합니다. **검색 및** 검색 **가능과** 같은 핵심 속성은 [저장소에 무시할 수 있는 영향을 미칩니다.](search-what-is-an-index.md#index-size) 필터를 사용하도록 설정하고 제안자를 사용하면 저장소 요구 사항이 증가합니다. 
   
   + **검색 가능하면** 전체 텍스트 검색을 사용할 수 있습니다. 자유 형식 쿼리 또는 쿼리 식에 사용되는 모든 필드에는 이 특성이 있어야 합니다. **검색 가능**으로 표시한 각 필드에 대해 반전된 인덱스가 만들어집니다.

   + **검색 가능은** 검색 결과에서 필드를 반환합니다. 검색 결과에 콘텐츠를 제공하는 모든 필드에는 이 특성이 있어야 합니다. 이 필드를 설정해도 인덱스 크기는 두드러지게 영향을 받지 않습니다.

   + **필터링 가능하면** 필터 식에서 필드를 참조할 수 있습니다. **$filter** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다. 필터 식은 정확한 일치를 위한 것입니다. 텍스트 문자열은 그대로 유지되므로 축자 콘텐츠를 포함하기 위한 추가 스토리지가 필요합니다.

   + **Facetable을** 사용하면 면탐색을 위한 필드를 사용할 수 있습니다. **필터링 가능**으로도 표시되는 필드만 **패싯 가능**으로 표시될 수 있습니다.

   + **정렬 가능하면** 필드를 일종의 로 사용할 수 있습니다. **$Orderby** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다.

1. [어휘 분석이](search-lucene-query-architecture.md#stage-2-lexical-analysis)필요하십니까? **검색 가능한**Edm.string 필드의 경우 언어가 향상된 인덱싱 및 쿼리를 원하는 경우 **분석기를** 설정할 수 있습니다. 

   기본값은 표준 Lucene이지만 불규칙한 명사 및 동사 형태를 확인하는 것과 같은 고급 어휘 처리에 Microsoft의 분석기를 사용하려는 경우 Microsoft 영어를 선택할 수도 있습니다.**** 포털에는 언어 분석기만 지정할 수 있습니다. 사용자 지정 분석기 또는 키워드, 패턴 등과 같은 비언어 분석기를 프로그래밍 방식으로 수행해야 합니다. 분석기에 대한 자세한 내용은 [언어 분석기 추가를](search-language-support.md)참조하십시오.

1. 자동 완성 또는 제안된 결과 의 형태로 typeahead 기능이 필요하십니까? 제안서 **를** 선택하여 typeahead 쿼리 제안을 활성화하고 선택한 필드에서 [자동 완성을 활성화합니다.](index-add-suggesters.md) 제안자는 인덱스의 토큰화된 용어 수에 추가되므로 더 많은 저장소를 소비합니다.


## <a name="next-steps"></a>다음 단계

마법사의 이점과 한계를 이해하는 가장 좋은 방법은 마법사를 단계별로 이해하는 것입니다. 다음 빠른 시작은 각 단계를 안내합니다.

> [!div class="nextstepaction"]
> [Azure 포털을 사용하여 Azure 인지 검색 인덱스 만들기](search-get-started-portal.md)