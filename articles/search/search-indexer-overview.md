<properties
	pageTitle="Azure 검색의 인덱서 | Microsoft Azure | 호스팅되는 클라우드 검색 서비스"
	description="검색 가능한 데이터를 추출하여 Azure 검색 인덱스에 입력하기 위해 Azure SQL 데이터베이스, DocumentDB 또는 Azure 저장소를 탐색합니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Azure 검색의 인덱서
> [AZURE.SELECTOR]
- [개요](search-indexer-overview.md)
- [포털](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Blob 저장소(미리 보기)](search-howto-indexing-azure-blob-storage.md)
- [테이블 저장소(미리 보기)](search-howto-indexing-azure-tables.md)

Azure 검색의 **인덱서**는 외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. 데이터를 인덱스에 푸시하기 위해 코드를 작성할 필요 없이 서비스가 데이터를 끌어오므로 이 방법을 종종 '끌어오기 모델'이라고도 합니다.

데이터 수집을 위한 유일한 방법으로 인덱서를 사용하거나, 인덱서 사용이 포함된 기술의 조합을 통해 인덱스의 일부 필드만 로드할 수 있습니다.

요청 시 또는 15분 간격으로 반복되는 데이터 새로 고침 예약에 따라 인덱서를 실행할 수 있습니다. 더 자주 업데이트하려면 Azure 검색과 외부 데이터 소스의 데이터를 동시에 업데이트하는 푸시 모델이 필요합니다.

## 인덱서를 만들고 관리하는 접근 방식

Azure SQL 또는 DocumentDB와 같은 일반적으로 사용 가능한 인덱서의 경우 다음과 같은 방법을 사용하여 인덱서를 만들고 관리할 수 있습니다.

- [포털 > 데이터 가져오기 마법사 ](search-get-started-portal.md)
- [서비스 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Azure Blob 또는 테이블 저장소와 같은 미리 보기 인덱서는 코드 및 [인덱서용 Azure 검색 미리 보기 REST API](search-api-indexers-2015-02-28-preview.md)와 같은 미리 보기 API가 필요합니다. 포털 도구는 일반적으로 미리 보기 기능에 사용할 수 없습니다.

## 기본 구성 단계

인덱서는 데이터 원본에 고유한 기능을 제공할 수 있습니다. 이러한 점에서 인덱서 또는 데이터 원본 구성의 일부 측면은 인덱서 유형에 따라 달라집니다. 그러나 인덱서는 모두 동일한 기본 구성 및 요구 사항을 공유합니다. 인덱서 모두에 공통되는 단계는 아래에서 다룹니다.

### 1단계: 인덱스 만들기

인덱서는 데이터 수집과 관련된 몇 가지 작업을 자동화하지만 인덱스를 만드는 작업은 포함되지 않습니다. 필수 구성 요소로서 외부 데이터 원본의 인덱스와 일치하는 필드를 포함한 미리 정의된 인덱스가 있어야 합니다. 인덱스를 구성하는 방법에 대한 자세한 내용은 [인덱스 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 참조하세요.

### 2단계: 데이터 원본 만들기

인덱서는 연결 문자열 등의 정보가 담긴 **데이터 원본**에서 데이터를 가져옵니다. 현재 지원되는 데이터 소스는 다음과 같습니다.

- [Azure SQL 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- PDF, Office 문서, HTML 또는 XML에서 텍스트를 추출하는 데 사용된 [Azure Blob 저장소(미리 보기)](search-howto-indexing-azure-blob-storage.md)
- [테이블 저장소(미리 보기)](search-howto-indexing-azure-tables.md)

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

### 3단계: 인덱서 만들기 및 예약

인덱서 정의는 인덱스, 데이터 원본 및 일정을 지정하는 구문입니다. 해당 데이터 원본이 동일한 구독에 있으면 인덱서도 다른 서비스에서 데이터 원본을 참조할 수 있습니다. 인덱서를 구성하는 방법에 대한 자세한 내용은 [인덱서 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx)를 참조하세요.

## 다음 단계

이제 기본 개념을 이해했으므로 다음 단계는 각 데이터 원본 유형과 관련된 요구 사항 및 작업을 검토하는 것입니다.

- [Azure SQL 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- PDF, Office 문서, HTML 또는 XML에서 텍스트를 추출하는 데 사용된 [Azure Blob 저장소(미리 보기)](search-howto-indexing-azure-blob-storage.md)
- [테이블 저장소(미리 보기)](search-howto-indexing-azure-tables.md)
- [Azure 검색 Blob 인덱서를 사용하여 CSV Blob 인덱싱(미리 보기)](search-howto-index-csv-blobs.md)
- [Azure 검색 Blob 인덱서를 사용하여 JSON Blob 인덱싱(미리 보기)](search-howto-index-json-blobs.md)

<!---HONumber=AcomDC_0810_2016-->