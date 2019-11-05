---
title: Azure Blob Storage에 전체 텍스트 검색 추가
titleSuffix: Azure Cognitive Search
description: Azure 인식에서 전체 텍스트 검색 인덱스를 작성할 때 콘텐츠를 추출 하 고 Azure blob에 구조를 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496491"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Azure Cognitive Search을 사용 하 여 Azure blob 데이터에 전체 텍스트 검색 추가

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 [Azure Cognitive Search](search-what-is-azure-search.md)를 사용 하 여 몇 번의 클릭 만으로 blob의 콘텐츠를 인덱싱하고 검색할 수 있습니다. Azure Cognitive Search에는 인덱싱에 데이터 원본 인식 기능을 추가 하는 [*blob 인덱서*](search-howto-indexing-azure-blob-storage.md) 를 통해 blob storage를 인덱싱하는 기본 제공 통합이 있습니다.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Blob 데이터에 전체 텍스트 검색을 추가 하는 것이 무엇을 의미 합니다.

Azure Cognitive Search는 검색 서비스에서 호스트 되는 사용자 정의 인덱스를 통해 작동 하는 인덱싱 및 쿼리 엔진을 제공 하는 클라우드 검색 서비스입니다. 클라우드의 쿼리 엔진과 함께 검색 가능한 콘텐츠를 공동 배치 하는 것이 성능에 필요 하며, 사용자가 검색 쿼리에서 제공 하는 속도에 따라 결과를 반환 합니다.

Azure Cognitive Search는 인덱싱 계층에서 Azure Blob storage와 통합 되어 blob 콘텐츠를 *반전 된 인덱스* 에 인덱싱된 검색 문서와 자유 형식 텍스트 쿼리 및 필터 식을 지 원하는 기타 쿼리 구조에 가져옵니다. . Blob 콘텐츠는 검색 인덱스로 인덱싱됩니다. blob 콘텐츠에 대 한 액세스는 Azure Cognitive Search의 전체 쿼리 기능을 활용할 수 있습니다.

인덱스를 만들고 채운 후에는 blob 컨테이너와 독립적으로 존재 하지만 인덱싱 작업을 다시 실행 하 여 기본 컨테이너의 변경 내용으로 인덱스를 새로 고칠 수 있습니다. 개별 blob에 대 한 타임 스탬프 정보는 변경 검색에 사용 됩니다. 예약 된 실행 또는 요청 시 인덱싱을 새로 고침 메커니즘으로 선택할 수 있습니다.

입력은 Azure Blob storage의 단일 컨테이너에 있는 blob입니다. Blob은 거의 모든 종류의 텍스트 데이터가 될 수 있습니다. Blob에 이미지가 포함 되어 있는 경우에는 [AI 보강을 blob 인덱싱에](search-blob-ai-integration.md) 추가 하 여 이미지에서 텍스트를 만들고 추출할 수 있습니다.

출력은 항상 빠른 텍스트 검색, 검색 및 클라이언트 응용 프로그램 탐색에 사용 되는 Azure Cognitive Search 인덱스입니다. 사이에는 인덱싱 파이프라인 아키텍처 자체가 있습니다. 파이프라인은이 문서에서 자세히 설명 하는 *인덱서* 기능을 기반으로 합니다.

## <a name="start-with-services"></a>서비스 시작

Azure Cognitive Search 및 Azure Blob storage가 필요 합니다. Blob storage 내에서 원본 콘텐츠를 제공 하는 컨테이너가 필요 합니다.

저장소 계정 포털 페이지에서 직접 시작할 수 있습니다. 왼쪽 탐색 페이지의 **Blob service** 에서 **Azure Cognitive Search 추가** 를 클릭 하 여 새 서비스를 만들거나 기존 서비스를 선택 합니다. 

Azure Cognitive Search를 저장소 계정에 추가한 후에는 표준 프로세스에 따라 blob 데이터를 인덱싱할 수 있습니다. 편리한 초기 소개를 위해 Azure Cognitive Search에서 **데이터 가져오기** 마법사를 사용 하는 것이 좋으며, postman과 같은 도구를 사용 하 여 REST api를 호출 하는 것이 좋습니다. 이 자습서에서는 [Azure Cognitive Search에서 Postman: 인덱스 및 검색 반 구조화 된 데이터 (JSON blob)](search-semi-structured-data.md)에서 REST API를 호출 하는 단계를 안내 합니다. 

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

*인덱서* 는 데이터를 샘플링 하 고, 메타 데이터 데이터를 읽고, 데이터를 검색 하 고, 후속 가져오기에 대 한 네이티브 형식에서 JSON 문서로 데이터를 직렬화 하는 내부 논리를 포함 하는 데이터 원본 인식 하위 서비스입니다. 

Azure Storage blob은 [Azure Cognitive Search blob 저장소 인덱서](search-howto-indexing-azure-blob-storage.md)를 사용 하 여 인덱싱됩니다. **데이터 가져오기** 마법사, REST API 또는 .net SDK를 사용 하 여이 인덱서를 호출할 수 있습니다. 코드에서는 형식을 설정 하 고 blob 컨테이너와 함께 Azure Storage 계정을 포함 하는 연결 정보를 제공 하 여이 인덱서를 사용 합니다. 가상 디렉터리를 만들어 매개 변수로 전달 하거나 파일 형식 확장을 필터링 하 여 blob의 하위 집합을 지정할 수 있습니다.

인덱서는 "문서 크랙"를 수행 하 여 콘텐츠를 검사 하는 blob을 엽니다. 데이터 원본에 연결한 후 파이프라인의 첫 번째 단계입니다. Blob 데이터의 경우에는 PDF, office 문서 및 기타 콘텐츠 형식이 검색 됩니다. 텍스트 추출을 사용한 문서 크랙는 무료로 제공 됩니다. Blob에 이미지 내용이 포함 되어 있는 경우 [AI 보강를 추가](search-blob-ai-integration.md)하지 않으면 이미지가 무시 됩니다. 표준 인덱싱은 텍스트 내용에만 적용 됩니다.

Blob 인덱서는 구성 매개 변수와 함께 제공 되며 기본 데이터가 충분 한 정보를 제공 하는 경우 변경 내용 추적을 지원 합니다. [Azure Cognitive Search Blob storage 인덱서의](search-howto-indexing-azure-blob-storage.md)핵심 기능에 대해 자세히 알아볼 수 있습니다.

### <a name="supported-content-types"></a>지원 되는 콘텐츠 형식

컨테이너에 대해 Blob 인덱서를 실행 하면 단일 쿼리를 사용 하 여 다음과 같은 콘텐츠 형식에서 텍스트 및 메타 데이터를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob 메타 데이터 인덱싱

모든 콘텐츠 형식의 blob을 쉽게 정렬할 수 있도록 하는 일반적인 시나리오는 각 blob에 대 한 사용자 지정 메타 데이터 및 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 모든 blob에 대 한 정보는 검색 서비스의 인덱스에 저장 된 문서 유형과 관계 없이 인덱싱됩니다. 그러면 새 인덱스를 사용 하 여 모든 Blob 저장소 콘텐츠에서 정렬, 필터링 및 패싯을 진행할 수 있습니다.

### <a name="indexing-json-blobs"></a>JSON blob 인덱싱
인덱서는 JSON을 포함 하는 blob에 있는 구조화 된 콘텐츠를 추출 하도록 구성할 수 있습니다. 인덱서는 JSON blob을 읽고 구조화 된 콘텐츠를 검색 문서의 적절 한 필드로 구문 분석할 수 있습니다. 인덱서는 JSON 개체의 배열을 포함 하는 blob을 사용 하 고 각 요소를 별도의 검색 문서에 매핑할 수도 있습니다. 구문 분석 모드를 설정 하 여 인덱서에서 만든 JSON 개체의 형식에 영향을 줄 수 있습니다.

## <a name="search-blob-content-in-a-search-index"></a>검색 인덱스에서 blob 콘텐츠 검색 

인덱싱의 출력은 클라이언트 앱에서 자유 텍스트 및 필터링 된 쿼리를 사용 하 여 대화형 탐색에 사용 되는 검색 인덱스입니다. 콘텐츠를 처음 탐색 하 고 확인 하려면 포털에서 [검색 탐색기](search-explorer.md) 로 시작 하 여 문서 구조를 검토 하는 것이 좋습니다. 검색 탐색기에서 [간단한 쿼리 구문](query-simple-syntax.md), [전체 쿼리 구문](query-lucene-syntax.md)및 [필터 식 구문을](query-odata-filter-orderby-syntax.md) 사용할 수 있습니다.

더 영구적인 솔루션은 쿼리 입력을 수집 하 고 클라이언트 응용 프로그램에서 검색 결과로 응답을 제공 하는 것입니다. 다음 C# 자습서에서는 검색 응용 프로그램을 빌드하는 방법에 대해 설명 합니다. [Azure Cognitive Search에서 첫 번째 응용 프로그램을 만듭니다](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>다음 단계

+ [Azure Portal를 사용 하 여 blob 업로드, 다운로드 및 나열 (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob 인덱서 설정 (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
