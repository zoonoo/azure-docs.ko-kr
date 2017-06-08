---
title: "Azure Search에 데이터 업로드 | Microsoft Docs"
description: "Azure 검색에서 인덱스에 데이터를 업로드하는 방법에 대해 알아봅니다."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="upload-data-to-azure-search"></a>Azure 검색에 데이터 업로드
> [!div class="op_single_selector"]
> * [개요](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST (영문)](search-import-data-rest-api.md)
> 
> 

데이터로 인덱스를 채우는 두 가지 방법이 있습니다. 첫 번째 옵션은 Azure 검색 [REST API](search-import-data-rest-api.md) 또는 [.NET SDK](search-import-data-dotnet.md)를 사용하여 데이터를 인덱스로 수동으로 밀어넣는 것입니다. 두 번째 옵션은 [지원되는 데이터 원본에서 인덱스를 가리키고](search-indexer-overview.md) Azure Search에서 데이터를 자동으로 끌어오도록 하는 것입니다.

## <a name="push-data-to-an-index"></a>인덱스에 데이터 푸시
이 방법은 검색에 사용할 수 있도록 프로그래밍 방식으로 데이터를 Azure 검색으로 보내는 것을 말합니다. 대기 시간 요구 사항이 매우 낮은 응용 프로그램의 경우(예: 검색 작업이 동적 재고 데이터베이스와 동기화되어야 하는 경우) 푸시 모델이 유일한 옵션입니다.

인덱스에 데이터를 푸시하려면 [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) 또는 [.NET SDK](search-import-data-dotnet.md)를 사용할 수 있습니다. 현재 포털을 통해 데이터를 푸시할 수 있는 도구가 없습니다.

개별적으로 또는 배치에서 문서를 업로드할 수 있기 때문에 이 접근 방식은 끌어오기 모델 보다 더 유연합니다(최대 배치 당 1000 또는 16MB, 모두 제한됨). 또한 밀어넣기 모델을 사용하면 데이터의 위치에 관계 없이 Azure 검색에 문서를 업로드할 수 있습니다.

Azure Search에서 판별할 수 있는 데이터 형식은 JSON이며, 데이터 집합의 모든 문서에는 인덱스 스키마에 정의된 필드에 매핑되는 필드가 있어야 합니다. 

## <a name="pull-data-into-an-index"></a>인덱스로 데이터를 끌어오기
끌어오기 모델은 지원되는 데이터 원본을 탐색하고 인덱스에 데이터를 자동으로 업로드합니다. Azure Search에서 이 기능은 *인덱서*를 통해 구현되며, 현재 [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [Azure VM의 Azure SQL Database 및 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)에 사용할 수 있습니다. 

인덱서는 인덱스를 데이터 원본(일반적으로 테이블, 뷰 또는 이와 동등한 구조)에 연결하고 원본 필드를 인덱스에 해당하는 필드에 매핑합니다. 실행 중에 행 집합은 자동으로 JSON으로 변환되고 지정한 인덱스에 로드됩니다. 모든 인덱서는 데이터를 새로 고치는 빈도를 지정할 수 있도록 일정 예약을 지원합니다. 대부분의 인덱서는 데이터 원본에서 지원하는 경우 변경 내용 추적을 제공합니다. 새 문서를 인식하는 것 외에도 기존 문서에서 변경 사항 및 삭제를 추적하므로 인덱서는 인덱스에서 데이터를 적극적으로 관리해야 할 필요를 없애줍니다. 

인덱서 기능은 [Azure Portal](search-import-data-portal.md), [REST API](/rest/api/searchservice/Indexer-operations) 및 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations)에서 공개되어 있습니다. 

포털을 사용하는 한 가지 장점으로 Azure Search에서 일반적으로 원본 데이터 집합의 메타데이터를 읽음으로써 기본 인덱스 스키마를 생성할 수 있다는 것입니다. 인덱스를 처리할 때까지 생성된 인덱스를 수정할 수 있으며, 그 후에는 다시 인덱싱할 필요가 없는 스키마만 편집할 수 있습니다. 스키마에 직접 적용하려는 변경 내용이 있으면 인덱스를 다시 작성해야 합니다. 

인덱스가 채워지면 포털 명령 모음에서 **검색 탐색기**를 확인 단계로 사용할 수 있습니다.

## <a name="query-an-index-using-search-explorer"></a>검색 탐색기를 사용하여 인덱스 쿼리

문서 업로드에 대한 사전 검사를 수행하는 빠른 방법은 포털에서 **검색 탐색기**를 사용하는 것입니다. 탐색기를 사용하면 코드를 작성할 필요 없이 인덱스를 쿼리할 수 있습니다. 검색 환경은 [간단한 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search) 및 기본 [searchMode 쿼리 매개 변수](/rest/api/searchservice/search-documents)를 기반으로 합니다. 결과는 JSON으로 반환되므로 전체 문서를 검사할 수 있습니다.

> [!TIP]
> 수많은 [Azure Search 코드 샘플](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)에는 포함되거나 쉽게 사용할 수 있는 데이터 집합이 있어 손쉽게 시작할 수 있는 방법을 제공합니다. 또한 포털에서는 작은 부동산 데이터 집합("realestate-us-sample")으로 구성된 샘플 인덱서 및 데이터 원본도 제공합니다. 샘플 데이터 원본에 미리 구성된 인덱서를 실행하면 인덱스가 [검색 탐색기] 또는 작성한 코드로 쿼리할 수 있는 문서로 만들어지고 로드됩니다.
