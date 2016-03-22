<properties
    pageTitle="Azure 검색에서 데이터 업로드 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 검색에서 인덱스에 데이터를 업로드하는 방법에 대해 알아봅니다."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"/>

# Azure 검색에 데이터 업로드
> [AZURE.SELECTOR]
- [개요](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST (영문)](search-import-data-rest-api.md)


## Azure 검색에서 데이터 업로드 모델
두 가지 방법으로 Azure 검색 인덱스를 데이터로 채울 수 있습니다. 첫 번째 옵션은 Azure 검색 [REST API](search-import-data-rest-api.md) 또는 [.NET SDK](search-import-data-dotnet.md)를 사용하여 데이터를 인덱스로 수동으로 밀어넣는 것입니다. 두 번째 옵션은 [지원되는 데이터 원본이](search-indexer-overview.md) Azure 검색 인덱스를 가리키도록 하고 Azure 검색에서 데이터를 검색 서비스로 자동으로 끌어오는 하는 것입니다.

이 가이드에서는 데이터 업로드의 밀어넣기 모델 사용([REST API](search-import-data-rest-api.md) 및 [.NET SDK](search-import-data-dotnet.md)에서만 지원됨)에 대한 지침만 다루지만, 아래 끌어오기 모델에 대해서도 학습할 수 있습니다.

### 인덱스에 데이터 푸시

이 방법은 검색에 사용할 수 있도록 프로그래밍 방식으로 데이터를 Azure 검색으로 보내는 것을 말합니다. 대기 시간 요구 사항이 매우 낮은 응용 프로그램의 경우(예: 동적 재고 데이터베이스와 동기화되도록 작업을 검색해야 하는 경우) 푸시 모델이 유일한 옵션입니다.

인덱스에 데이터를 푸시하려면 [REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx) 또는 [.NET SDK](search-import-data-dotnet.md)를 사용할 수 있습니다. 현재 포털을 통해 데이터를 푸시할 수 있는 도구가 없습니다.

개별적으로 또는 배치에서 문서를 업로드할 수 있기 때문에 이 접근 방식은 끌어오기 모델 보다 더 유연합니다(최대 배치 당 1000 또는 16MB, 모두 제한됨). 또한 밀어넣기 모델을 사용하면 데이터의 위치에 관계 없이 Azure 검색에 문서를 업로드할 수 있습니다.

### 인덱스로 데이터를 끌어오기

끌어오기 모델은 지원되는 데이터 원본을 탐색하고 데이터를 Azure 검색 인덱스에 자동으로 업로드합니다. 새 문서를 인식하는 것 외에도 기존 문서에서 변경 사항 및 삭제를 추적하므로 인덱서는 인덱스에서 데이터를 적극적으로 관리해야 할 필요를 없애줍니다.

Azure 검색에서 이 기능은 *인덱서*를 통해 구현되며 [Blob 저장소(미리 보기)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Azure SQL 데이터베이스 및 Azure VM의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)에 사용할 수 있습니다.

인덱서 기능은 [Azure 포털](search-import-data-portal.md)뿐만 아니라 [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)에서도 노출됩니다.

<!---HONumber=AcomDC_0316_2016-->