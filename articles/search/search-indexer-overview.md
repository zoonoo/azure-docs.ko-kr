---
title: "Azure Search의 인덱서 | Microsoft Docs"
description: "검색 가능한 데이터를 추출하여 Azure Search 인덱스에 입력하기 위해 Azure SQL Database, Azure Cosmos DB 또는 Azure Storage를 탐색합니다."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 1ea9dfcaec188bac2e92909f4811568b589357d4
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---

# <a name="indexers-in-azure-search"></a>Azure 검색의 인덱서
> [!div class="op_single_selector"]
>
> * [개요](search-indexer-overview.md)
> * [포털](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
> * [Azure 테이블 저장소](search-howto-indexing-azure-tables.md)
>
>

Azure 검색의 **인덱서** 는 외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. 데이터를 인덱스에 푸시하기 위해 코드를 작성할 필요 없이 서비스가 데이터를 끌어오므로 이 방법을 종종 '끌어오기 모델'이라고도 합니다.

데이터 수집을 위한 유일한 방법으로 인덱서를 사용하거나, 인덱서 사용이 포함된 기술의 조합을 통해 인덱스의 일부 필드만 로드할 수 있습니다.

요청 시 또는 15분 간격으로 반복되는 데이터 새로 고침 예약에 따라 인덱서를 실행할 수 있습니다. 더 자주 업데이트하려면 Azure 검색과 외부 데이터 소스의 데이터를 동시에 업데이트하는 푸시 모델이 필요합니다.

## <a name="approaches-for-creating-and-managing-indexers"></a>인덱서를 만들고 관리하는 접근 방식
Azure SQL 또는 Azure Cosmos DB와 같은 일반적으로 사용 가능한 인덱서의 경우 다음과 같은 방법을 사용하여 인덱서를 만들고 관리할 수 있습니다.

* [포털 > 데이터 가져오기 마법사](search-get-started-portal.md)
* [서비스 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>기본 구성 단계
인덱서는 데이터 원본에 고유한 기능을 제공할 수 있습니다. 이러한 점에서 인덱서 또는 데이터 원본 구성의 일부 측면은 인덱서 유형에 따라 달라집니다. 그러나 인덱서는 모두 동일한 기본 구성 및 요구 사항을 공유합니다. 인덱서 모두에 공통되는 단계는 아래에서 다룹니다.

### <a name="step-1-create-an-index"></a>1단계: 인덱스 만들기
인덱서는 데이터 수집과 관련된 몇 가지 작업을 자동화하지만 인덱스를 만드는 작업은 포함되지 않습니다. 필수 구성 요소로서 외부 데이터 원본의 인덱스와 일치하는 필드를 포함한 미리 정의된 인덱스가 있어야 합니다. 인덱스를 구성하는 방법에 대한 자세한 내용은 [인덱스 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 참조하세요.

### <a name="step-2-create-a-data-source"></a>2단계: 데이터 원본 만들기
인덱서는 연결 문자열 등의 정보가 담긴 **데이터 원본** 에서 데이터를 가져옵니다. 현재 지원되는 데이터 소스는 다음과 같습니다.

* [Azure SQL 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* 선택한 콘텐츠 유형에 대한 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure 테이블 저장소](search-howto-indexing-azure-tables.md)

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

### <a name="step-3create-and-schedule-the-indexer"></a>3단계: 인덱서 만들기 및 예약
인덱서 정의는 인덱스, 데이터 원본 및 일정을 지정하는 구문입니다. 해당 데이터 원본이 동일한 구독에 있으면 인덱서도 다른 서비스에서 데이터 원본을 참조할 수 있습니다. 인덱서를 구성하는 방법에 대한 자세한 내용은 [인덱서 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 기본 개념을 이해했으므로 다음 단계는 각 데이터 원본 유형과 관련된 요구 사항 및 작업을 검토하는 것입니다.

* [Azure SQL 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure 테이블 저장소](search-howto-indexing-azure-tables.md)
* [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
* [Azure Search Blob 인덱서를 사용하여 JSON Blob 인덱싱](search-howto-index-json-blobs.md)

