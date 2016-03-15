<properties
	pageTitle="Azure 검색의 인덱서 | Microsoft Azure | 호스팅되는 클라우드 검색 서비스"
	description="검색 가능한 데이터를 추출하여 Azure 검색 인덱스에 입력하기 위해 데이터베이스 탐색"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="03/09/2016"
	ms.author="heidist"/>

# Azure 검색의 인덱서
> [AZURE.SELECTOR]
- [개요](search-indexer-overview.md)
- [포털](search-import-data-portal.md)
- [Blob 저장소(미리 보기)](search-howto-indexing-azure-blob-storage.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)

Azure 검색의 **인덱서**는 외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. 데이터를 인덱스에 푸시하기 위해 코드를 작성할 필요 없이 서비스가 데이터를 끌어오므로 이 방법을 종종 '끌어오기 모델'이라고도 합니다.

데이터 수집을 위한 유일한 방법으로 인덱서를 사용하거나, 인덱서 사용이 포함된 기술의 조합을 통해 인덱스의 일부 필드만 로드할 수 있습니다.

인덱서는 요청 시 또는 반복되는 데이터 새로 고침 예약에 따라 15분 간격으로 실행될 수 있습니다. 더 자주 업데이트하려면 Azure 검색과 외부 데이터 소스의 데이터를 동시에 업데이트하는 푸시 모델이 필요합니다.

인덱서는 연결 문자열 등의 정보가 담긴 **데이터 원본**에서 데이터를 가져옵니다. 현재 지원되는 데이터 소스는 다음과 같습니다.

- [Azure SQL 데이터베이스](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)(또는 Azure 가상 컴퓨터의 SQL Server)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob 저장소](search-howto-indexing-azure-blob-storage.md)(현재 미리 보기로 제공. PDF, Office 문서, HTML, XML에서 텍스트 추출).

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

[.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) 및 [서비스 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) 모두 인덱서 및 데이터 원본을 관리하도록 지원합니다.

또는 **데이터 가져오기** 마법사를 사용하는 경우 포털에서 인덱서를 구성할 수 있습니다. 간략한 자습서는 [포털에서 Azure 검색 시작](search-get-started-portal)을 참조하세요. 마법사를 사용하여 인덱스를 만들고 로드하도록 샘플 데이터 및 DocumentDB 인덱서를 사용합니다.

<!---HONumber=AcomDC_0309_2016-->