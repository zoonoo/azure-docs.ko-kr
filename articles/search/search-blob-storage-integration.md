---
title: Azure Blob 저장소에 전체 텍스트 검색 추가
titleSuffix: Azure Cognitive Search
description: Azure Cognitive earch에서 전체 텍스트 검색 인덱스를 빌드할 때 콘텐츠를 추출하고 Azure Blob에 구조를 추가합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496491"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Azure 인지 검색을 사용하여 Azure Blob 데이터에 전체 텍스트 검색 추가

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 [Azure Cognitive Search를](search-what-is-azure-search.md)사용하여 몇 번의 클릭만으로 Blob의 콘텐츠를 인덱싱하고 검색할 수 있습니다. Azure Cognitive Search에는 데이터 원본 인식 기능을 인덱싱에 추가하는 [*Blob 인덱서를*](search-howto-indexing-azure-blob-storage.md) 통해 Blob 저장소에서 인덱싱하기 위한 통합이 내장되어 있습니다.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Blob 데이터에 전체 텍스트 검색을 추가한다는 의미

Azure Cognitive Search는 검색 서비스에서 호스팅되는 사용자 정의 인덱스를 통해 작동하는 인덱싱 및 쿼리 엔진을 제공하는 클라우드 검색 서비스입니다. 검색 가능한 콘텐츠를 클라우드의 쿼리 엔진과 함께 배치하는 것은 성능을 위해 필요하며 사용자가 검색 쿼리에서 기대하는 속도로 결과를 반환합니다.

Azure Cognitive Search는 인덱싱 계층의 Azure Blob 저장소와 통합되어 *반전된 인덱스* 및 자유 형식 텍스트 쿼리 및 필터 식을 지원하는 기타 쿼리 구조로 인덱싱되는 검색 문서로 Blob 콘텐츠를 가져옵니다. Blob 콘텐츠가 검색 인덱스로 인덱싱되므로 Blob 콘텐츠에 대한 액세스는 Azure Cognitive Search의 모든 쿼리 기능을 활용할 수 있습니다.

인덱스가 만들어지고 채워지면 Blob 컨테이너와 독립적으로 존재하지만 인덱싱 작업을 다시 실행하여 기본 컨테이너를 변경하여 인덱스를 새로 고칠 수 있습니다. 개별 Blob에 대한 타임스탬프 정보는 변경 검색에 사용됩니다. 예약된 실행 또는 온디맨드 인덱싱을 새로 고침 메커니즘으로 선택할 수 있습니다.

입력은 Azure Blob 저장소의 단일 컨테이너에 있는 Blob입니다. Blob은 거의 모든 종류의 텍스트 데이터일 수 있습니다. Blob에 이미지가 포함된 경우 [Blob 인덱싱에 AI 보강을](search-blob-ai-integration.md) 추가하여 이미지에서 텍스트를 만들고 추출할 수 있습니다.

출력은 항상 클라이언트 응용 프로그램에서 빠른 텍스트 검색, 검색 및 탐색에 사용되는 Azure Cognitive Search 인덱스입니다. 그 사이에는 인덱싱 파이프라인 아키텍처 자체가 있습니다. 파이프라인은 이 문서에서 자세히 설명한 *인덱서* 기능을 기반으로 합니다.

## <a name="start-with-services"></a>서비스 시작

Azure 인지 검색 및 Azure Blob 저장소가 필요합니다. Blob 저장소 내에 원본 콘텐츠를 제공하는 컨테이너가 필요합니다.

Storage 계정 포털 페이지에서 직접 시작할 수 있습니다. 왼쪽 탐색 페이지에서 **Blob 서비스** 아래에서 **Azure 인지 검색 추가를** 클릭하여 새 서비스를 만들거나 기존 서비스를 선택합니다. 

저장소 계정에 Azure Cognitive Search를 추가하면 표준 프로세스를 따라 Blob 데이터를 인덱싱할 수 있습니다. Azure Cognitive Search의 **데이터 가져오기** 마법사를 사용하여 쉽게 초기 소개하거나 Postman과 같은 도구를 사용하여 REST API를 호출하는 것이 좋습니다. 이 자습서에서는 우체부에서 REST API: [색인 및 검색 반구조화 데이터(JSON Blob)를 Azure 인지 검색에서](search-semi-structured-data.md)호출하는 단계를 안내합니다. 

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

*인덱서에는* 데이터 샘플링, 메타데이터 데이터 읽기, 데이터 검색, 네이티브 형식의 데이터를 JSON 문서로 직렬화하여 이후 가져오기를 위한 내부 논리가 장착된 데이터 원본 인식 하위 서비스입니다. 

Azure 저장소의 Blob은 Azure [인지 검색 Blob 저장소 인덱서를](search-howto-indexing-azure-blob-storage.md)사용하여 인덱싱됩니다. **가져오기 데이터** 마법사, REST API 또는 .NET SDK를 사용하여 이 인덱서를 호출할 수 있습니다. 코드에서는 형식을 설정하고 Blob 컨테이너와 함께 Azure Storage 계정을 포함하는 연결 정보를 제공하여 이 인덱서를 사용합니다. 그런 다음 매개 변수로 전달할 수 있는 가상 디렉터리를 만들거나 파일 형식 확장명에 필터링하여 Blob을 하위집합할 수 있습니다.

인덱서가 "문서 크래킹"을 수행하여 Blob을 열어 내용을 검사합니다. 데이터 원본에 연결한 후 파이프라인의 첫 번째 단계입니다. Blob 데이터의 경우 PDF, 사무실 문서 및 기타 콘텐츠 형식이 검색되는 위치입니다. 텍스트 추출을 통해 문서 크래킹은 무료입니다. Blob에 이미지 콘텐츠가 포함된 경우 [AI 보강을 추가하지](search-blob-ai-integration.md)않는 한 이미지가 무시됩니다. 표준 인덱싱은 텍스트 콘텐츠에만 적용됩니다.

Blob 인덱서에는 구성 매개 변수가 함께 제공되며 기본 데이터가 충분한 정보를 제공하는 경우 변경 사항 추적을 지원합니다. [Azure 인지 검색 Blob 저장소 인덱서에서](search-howto-indexing-azure-blob-storage.md)핵심 기능에 대해 자세히 알아볼 수 있습니다.

### <a name="supported-content-types"></a>지원되는 콘텐츠 유형

컨테이너 위에 Blob 인덱서를 실행하여 단일 쿼리를 통해 다음 콘텐츠 형식에서 텍스트 및 메타데이터를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob 메타데이터 인덱싱

모든 콘텐츠 형식의 Blob을 쉽게 정렬할 수 있도록 하는 일반적인 시나리오는 각 Blob에 대한 사용자 지정 메타데이터와 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 모든 Blob에 대한 정보는 문서 유형에 관계없이 인덱싱되며 검색 서비스의 인덱스에 저장됩니다. 그런 다음 새 인덱스를 사용하여 모든 Blob 저장소 콘텐츠에서 정렬, 필터링 및 면을 진행할 수 있습니다.

### <a name="indexing-json-blobs"></a>JSON Blob 인덱싱
인덱서를 구성하여 JSON을 포함하는 Blob에 있는 구조화 된 콘텐츠를 추출할 수 있습니다. 인덱서 작성자는 JSON Blob을 읽고 구조화된 내용을 검색 문서의 해당 필드로 구문 분석할 수 있습니다. 인덱서에는 JSON 개체 배열이 포함된 Blob을 가져와서 각 요소를 별도의 검색 문서에 매핑할 수도 있습니다. 인덱서에서 만든 JSON 개체의 유형에 영향을 주는 구문 분석 모드를 설정할 수 있습니다.

## <a name="search-blob-content-in-a-search-index"></a>검색 인덱스에서 Blob 콘텐츠 검색 

인덱싱의 출력은 클라이언트 앱에서 자유 텍스트 및 필터링된 쿼리를 사용하여 대화형 탐색에 사용되는 검색 인덱스입니다. 콘텐츠의 초기 탐색 및 확인을 위해 포털에서 [검색 탐색기로](search-explorer.md) 시작하여 문서 구조를 검사하는 것이 좋습니다. Search 탐색기에서 [간단한 쿼리 구문,](query-simple-syntax.md) [전체 쿼리 구문](query-lucene-syntax.md)및 [필터 식 구문을](query-odata-filter-orderby-syntax.md) 사용할 수 있습니다.

보다 영구적인 해결책은 쿼리 입력을 수집하고 응답을 클라이언트 응용 프로그램에서 검색 결과로 표시하는 것입니다. 다음 C# 자습서에서는 검색 응용 프로그램을 [Create your first application in Azure Cognitive Search](tutorial-csharp-create-first-app.md)빌드하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure 포털(Azure Blob 저장소)을 통해 Blob을 업로드, 다운로드 및 나열합니다.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob 인덱서 설정(Azure 인지 검색)](search-howto-indexing-azure-blob-storage.md) 
