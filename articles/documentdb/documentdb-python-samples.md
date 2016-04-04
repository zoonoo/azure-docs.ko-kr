<properties 
	pageTitle="DocumentDB용 NoSQL Python 예제 | Microsoft Azure" 
	description="NoSQL 데이터베이스에서 JSON 문서에 대한 CRUD 작업을 비롯한 DocumentDB의 일반적인 작업에 대한 github의 NoSQL Python 예제를 찾습니다." 
	keywords="Python 예제"
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="python"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/22/2016" 
	ms.author="ryancraw"/>


# DocumentDB Python 예제

> [AZURE.SELECTOR]
- [.NET 예제](documentdb-dotnet-samples.md)
- [Node.js 예제](documentdb-nodejs-samples.md)
- [Python 예제](documentdb-python-samples.md)
- [Azure 코드 샘플 갤러리](https://azure.microsoft.com/documentation/samples/?service=documentdb)

DocumentDB 리소스에 대한 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) GitHub 리포지토리에 포함됩니다. 이 문서는 다음을 제공합니다.

- 각 Python 예제 프로젝트 파일에서 작업에 연결합니다. 
- 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

1. 이러한 Python 예제를 사용하려면 Azure 계정이 필요합니다.
    - [Azure 계정을 무료로 개설](https://azure.microsoft.com/pricing/free-trial/)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.
   - [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)할 수 있음: Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
2. 또한 [Python SDK](documentdb-sdk-python.md)가 필요합니다. 

    > [AZURE.NOTE] 각 샘플은 자체 포함되며 자체를 설정하고 자체를 정리합니다. 샘플은 [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컬렉션의 성능 계층 당 1시간 사용량이 청구됩니다.

## 데이터베이스 예제

[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[데이터베이스 만들기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document\_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[데이터베이스에 대한 계정 쿼리](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document\_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[ID에서 데이터베이스 읽기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document\_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document\_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[데이터베이스 삭제](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document\_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## 컬렉션 예제 

[CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[컬렉션 만들기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[데이터베이스에서 모든 컬렉션의 목록 읽기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document\_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[ID에서 컬렉션 가져오기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document\_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[컬렉션의 성능 계층 가져오기](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[컬렉션의 성능 계층 변경](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document\_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[컬렉션 삭제](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document\_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)

<!---HONumber=AcomDC_0323_2016-->