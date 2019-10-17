---
title: Azure Blob Storage에 전체 텍스트 검색 추가
titleSuffix: Azure Search
description: Azure Search에서 전체 텍스트 검색 인덱스를 작성할 때 콘텐츠를 추출 하 고 Azure blob에 구조를 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331286"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Azure Search를 사용 하 여 Azure blob 데이터에 전체 텍스트 검색 추가

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 [Azure Search](search-what-is-azure-search.md)를 사용 하 여 몇 번의 클릭 만으로 blob의 콘텐츠를 인덱싱하고 검색할 수 있습니다. Azure Search에는 인덱싱에 데이터 원본 인식 기능을 추가 하는 [*blob 인덱서*](search-howto-indexing-azure-blob-storage.md) 를 통해 blob storage를 인덱싱하는 기본 제공 통합이 있습니다.

## <a name="supported-content-types"></a>지원 되는 콘텐츠 형식

컨테이너에 대해 Blob 인덱서를 실행 하면 단일 쿼리를 사용 하 여 다음과 같은 콘텐츠 형식에서 텍스트 및 메타 데이터를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

필요에 따라 [AI 보강](search-blob-ai-integration.md) 을 *기술* 형식으로 연결 하 여 이미지 파일의 텍스트 표현을 포함 하 여 blob에서 새 정보 및 구조를 만들 수 있습니다. AI 보강 추가 하면 콘텐츠 형식 목록이 JPEG 및 PNG를 포함 하도록 확장 됩니다. [OCR (광학 문자 인식)](cognitive-search-skill-ocr.md) 및 [시각적 기능](cognitive-search-skill-image-analysis.md) 식별과 같은 이미지 처리 기술을 추가 하 여 각 이미지에 있는 시각적 콘텐츠를 인덱싱할 수 있도록 합니다.

## <a name="search-through-your-blob-metadata"></a>Blob 메타데이터에서 검색
모든 콘텐츠 형식의 BLOB을 손쉽게 정렬할 수 있는 일반적인 시나리오는 각 BLOB에 대해 사용자 지정 메타데이터와 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 모든 blob에 대 한 정보는 Azure Search의 인덱스에 저장 된 문서 유형과 관계 없이 인덱싱됩니다. 그러면 새 인덱스를 사용 하 여 모든 Blob 저장소 콘텐츠에서 정렬, 필터링 및 패싯을 진행할 수 있습니다.

### <a name="indexing-json-blobs"></a>JSON blob 인덱싱
JSON이 포함된 Blob에 있는 구조화된 콘텐츠를 추출하도록 Azure Search를 구성할 수 있습니다. Azure Search는 JSON Blob을 읽고, 구조화된 콘텐츠를 Azure Search 문서의 적절한 필드로 구문 분석합니다. Azure Search는 또한 JSON 개체의 배열을 포함하는 Blob을 처리하고 각 요소를 개별 Azure Search 문서에 매핑할 수 있습니다. 구문 분석 모드를 설정 하 여 인덱서에서 만든 JSON 개체의 형식에 영향을 줄 수 있습니다.

## <a name="how-to-get-started"></a>시작 방법

저장소 계정 포털 페이지에서 직접 시작할 수 있습니다. **Azure Search 추가**를 클릭하면 기존 Azure Search 서비스를 선택하거나 새 서비스를 만들 수 있는 흐름이 시작됩니다. 새 서비스를 만드는 경우 스토리지 계정의 포털 환경 외부로 이동하게 됩니다. 기존 서비스를 선택할 수 있는 스토리지 포털 블레이드로 다시 이동하여 **Azure Search 추가** 옵션을 다시 선택할 수 있습니다. 

![](./media/search-blob-storage-integration/blob-blade.png)

동일한 구독에 기존 검색 서비스가 이미 있는 경우 **추가 Azure Search** 클릭 하면 인덱싱, 보강 및 인덱스 정의를 즉시 단계별로 수행할 수 있도록 데이터 가져오기 마법사가 열립니다.

[Azure Search 서비스를 만들고](search-create-index-portal.md) blob 컨테이너에서 콘텐츠를 가져오는 blob 인덱서를 정의할 수도 있습니다.

다음 퀵 스타트 및 자습서에서는 Blob 데이터를 사용 합니다.

+ [REST Api를 사용 하 여 반 구조화 된 blob 인덱싱](search-semi-structured-data.md)
+ [포털에서 AI 보강 파이프라인 만들기](cognitive-search-quickstart-blob.md)
+ [에서 AI 보강 파이프라인 만들기C#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Blob 인덱서 설정](search-howto-indexing-azure-blob-storage.md) 
