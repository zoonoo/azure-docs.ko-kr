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
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312190"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Azure Search를 사용 하 여 Azure blob 데이터에 전체 텍스트 검색 추가

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 [Azure Search](search-what-is-azure-search.md)를 사용 하 여 몇 번의 클릭 만으로 blob의 콘텐츠를 인덱싱하고 검색할 수 있습니다. Azure Search에는 인덱싱에 데이터 원본 인식 기능을 추가 하는 [*blob 인덱서*](search-howto-indexing-azure-blob-storage.md) 를 통해 blob storage를 인덱싱하는 기본 제공 통합이 있습니다.

## <a name="supported-content-types"></a>지원 되는 콘텐츠 형식

컨테이너에 대해 Blob 인덱서를 실행 하면 단일 쿼리를 사용 하 여 다음과 같은 콘텐츠 형식에서 텍스트 및 메타 데이터를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

필요에 따라 [AI 보강](search-blob-ai-integration.md) 을 *기술* 형식으로 연결 하 여 이미지 파일의 텍스트 표현을 포함 하 여 blob에서 새 정보 및 구조를 만들 수 있습니다. AI 보강 추가 하면 콘텐츠 형식 목록이 JPEG 및 PNG를 포함 하도록 확장 됩니다. [OCR (광학 문자 인식)](cognitive-search-skill-ocr.md) 및 [시각적 기능](cognitive-search-skill-image-analysis.md) 식별과 같은 이미지 처리 기술을 추가 하 여 각 이미지에 있는 시각적 콘텐츠를 인덱싱할 수 있도록 합니다.

## <a name="search-through-your-blob-metadata"></a>Blob 메타데이터에서 검색
모든 콘텐츠 형식의 BLOB을 손쉽게 정렬할 수 있는 일반적인 시나리오는 각 BLOB에 대해 사용자 지정 메타데이터와 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 문서 형식에 관계없이 모든 BLOB을 인덱싱할 수 있습니다. 그런 다음 모든 Blob Storage 콘텐츠에서 정렬, 필터링, 패싯을 수행할 수 있습니다.

[Blob 메타데이터 인덱싱에 관해 자세히 알아보세요.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>JSON Blob을 통해 인덱스 만들기 및 검색
JSON이 포함된 Blob에 있는 구조화된 콘텐츠를 추출하도록 Azure Search를 구성할 수 있습니다. Azure Search는 JSON Blob을 읽고, 구조화된 콘텐츠를 Azure Search 문서의 적절한 필드로 구문 분석합니다. Azure Search는 또한 JSON 개체의 배열을 포함하는 Blob을 처리하고 각 요소를 개별 Azure Search 문서에 매핑할 수 있습니다.

JSON 구문 분석은 현재 포털을 통해 구성할 수 없습니다. [Azure Search에서 JSON 구문 분석에 대해 자세히 알아보세요.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>빠른 시작
Azure Search는 Blob Storage 포털 블레이드에서 직접 BLOB에 추가할 수 있습니다.

![](./media/search-blob-storage-integration/blob-blade.png)

**Azure Search 추가**를 클릭하면 기존 Azure Search 서비스를 선택하거나 새 서비스를 만들 수 있는 흐름이 시작됩니다. 새 서비스를 만드는 경우 스토리지 계정의 포털 환경 외부로 이동하게 됩니다. 기존 서비스를 선택할 수 있는 스토리지 포털 블레이드로 다시 이동하여 **Azure Search 추가** 옵션을 다시 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계
전체 [설명서](https://aka.ms/azsblobindexer)에서 Azure Search Blob Indexer에 대해 자세히 알아봅니다.
