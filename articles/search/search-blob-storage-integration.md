---
title: "Blob Storage에 Azure Search 추가 | Microsoft Docs"
description: "Azure 검색 HTTP REST API를 사용하여 코드에 인덱스를 만듭니다."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Azure Search로 Blob Storage 검색

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 Azure Search를 사용하면 단 몇 번의 클릭으로 Blob의 콘텐츠에 인덱스를 만들고 검색할 수 있습니다. Blob Storage를 통해 검색하려면 Azure Search 서비스를 프로비전해야 합니다. Azure Search의 다양한 서비스 한도 및 가격 책정 계층은 [가격 책정 페이지](https://aka.ms/azspricing)에서 확인할 수 있습니다.

## <a name="what-is-azure-search"></a>Azure 검색이란?
[Azure Search](https://aka.ms/whatisazsearch)란 개발자가 강력한 전체 텍스트 검색 환경을 웹 및 모바일 응용 프로그램에 손쉽게 추가할 수 있도록 하는 검색 솔루션입니다. 서비스와 같이 Azure Search는 [99.9% 작동 시간 SLA](https://aka.ms/azuresearchsla)를 제공하면서 검색 인프라를 관리할 필요가 없습니다.

Azure Search는 56개 언어의 고급 지원을 통해 사용자의 콘텐츠를 분석하고 언어별 구조를 지능적으로 처리할 수 있습니다. Azure Search는 또한 풍부한 검색 사용자 환경을 빌드하기 위한 도구를 제공합니다. Azure Search를 사용하여 패싯 탐색, 미리 입력 검색 제안 및 적중 항목 강조 표시와 같은 기능을 간단히 사용자 인터페이스에 추가할 수 있습니다. Azure Search의 기능은 서비스의 [설명서](https://aka.ms/azsearchdocs)에서 확인할 수 있습니다.

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>Crack 열기 및 엔터프라이즈 문서 형식의 콘텐츠 검색
Azure Blob Storage의 [문서 추출](https://aka.ms/azsblobindexer) 지원을 통해 Azure Search는 Blob에 저장된 다양한 파일 형식의 콘텐츠에 인덱스를 만들 수 있습니다.
- PDF
- Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG(Outlook 메일)
- HTML
- 일반 텍스트 파일

이러한 파일 형식의 텍스트 및 메타데이터를 추출하면 한 번의 쿼리로 여러 파일 형식 전반을 검색하여 형식에 관계 없이 가장 관련성이 높은 문서를 손쉽게 찾을 수 있습니다. Microsoft Office 문서, PDF 및 메일의 콘텐츠 및 메타데이터를 인덱싱하면 Blob Storage 및 Azure Search를 사용하여 강력한 엔터프라이즈 콘텐츠 관리 솔루션을 구축할 수 있습니다.

## <a name="search-through-your-blob-metadata"></a>Blob 메타데이터에서 검색
모든 콘텐츠 형식의 Blob을 손쉽게 정렬할 수 있는 일반적인 시나리오는 사용자의 각 Blob에 대한 시스템 속성뿐 아니라 사용자 지정, 사용자 정의 Blob 메타데이터에 인덱스를 만드는 것입니다. 이렇게 하면 모든 각 Blob에 대한 정보는 Blob의 문서 형식에 관계 없이 인덱스가 만들어지므로 모든 Blob Storage 콘텐츠 전반에 걸쳐 손쉽게 정렬 및 패싯할 수 있습니다.

[Blob 메타데이터 인덱싱에 관해 자세히 알아보세요.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>이미지 검색
Azure Search의 전체 텍스트 검색, 패싯 탐색 및 정렬 기능을 이제 Blob에 저장된 이미지의 메타데이터에 적용할 수 있습니다.

이러한 이미지가 Microsoft Cognitive Services에서 [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api)를 사용하여 전처리되면, OCR 및 필기 인식 기능을 포함하는 각 이미지에 있는 시각적 콘텐츠에 인덱스를 만들 수 있습니다. 현재 OCR 및 다른 이미지 처리 기능을 직접 Azure Search에 추가하는 작업이 진행 중입니다. 이러한 기능에 관심이 있다면 [UserVoice](https://aka.ms/azsuv) 또는 [메일로](mailto:azscustquestions@microsoft.com) 요청을 제출해 주세요.

## <a name="index-and-search-through-json-blobs"></a>JSON Blob을 통해 인덱스 만들기 및 검색
JSON이 포함된 Blob에 있는 구조화된 콘텐츠를 추출하도록 Azure Search를 구성할 수 있습니다. Azure Search는 JSON Blob을 읽고, 구조화된 콘텐츠를 Azure Search 문서의 적절한 필드로 구문 분석합니다. Azure Search는 또한 JSON 개체의 배열을 포함하는 Blob을 처리하고 각 요소를 개별 Azure Search 문서에 매핑할 수 있습니다.

JSON 구문 분석은 현재 포털을 통해 구성할 수 없습니다. [Azure Search에서 JSON 구문 분석에 대해 자세히 알아보세요.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>빠른 시작
Azure Search는 Blob Storage 포털 블레이드에서 직접 Blob에 추가할 수 있습니다.

![](./media/search-blob-storage-integration/blob-blade.png)

“Azure Search 추가” 옵션을 클릭하면 기존 Azure Search 서비스를 선택하거나 새 서비스를 만들 수 있는 흐름이 시작됩니다. 새 서비스를 만드는 경우 저장소 계정의 포털 환경 외부로 이동하게 됩니다. 기존 서비스를 선택할 수 있는 저장소 포털 블레이드로 다시 이동하여 “Azure Search 추가” 옵션을 다시 선택해야 합니다.

### <a name="next-steps"></a>다음 단계
전체 [설명서](https://aka.ms/azsblobindexer)에서 Azure Search Blob Indexer에 대해 자세히 알아봅니다.

