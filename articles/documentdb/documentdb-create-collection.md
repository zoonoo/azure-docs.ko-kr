<properties 
	pageTitle="DocumentDB 데이터베이스 컬렉션 만들기 | Microsoft Azure" 
	description="Azure DocumentDB용 온라인 서비스 포털, JSON용 관리되는 NoSQL 문서 데이터베이스를 사용하여 컬렉션을 만드는 방법을 알아봅니다. 지금 무료 평가판을 사용해보세요." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="mimig"/>

# DocumentDB 컬렉션 만들기

Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), [데이터베이스](documentdb-create-database.md), 컬렉션 및 문서가 있어야 합니다. 이 항목에서는 Azure 포털에서 DocumentDB 컬렉션을 만드는 방법을 설명합니다.

![이동 표시줄에서 DocumentDB 계정을, DocumentDB 계정 블레이드에서 계정을, DocumentDB 계정 블레이드와 데이터베이스 렌즈에서 데이터베이스를 강조 표시한 스크린샷](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB 계정**을 클릭합니다. 

2.  **DocumentDB 계정** 블레이드에서 컬렉션을 추가할 계정을 선택합니다. 나열된 계정이 없는 경우 [DocumentDB 계정을 만들](documentdb-create-account.md)어야 합니다.

3. 선택한 계정에 대한 **DocumentDB 계정** 블레이드에서 **데이터베이스** 렌즈까지 아래로 스크롤하여 컬렉션을 추가할 데이터베이스를 선택합니다.
    
4. **데이터베이스** 블레이드에서 **컬렉션 추가**를 클릭합니다.

	![데이터베이스 블레이드의 컬렉션 추가 단추, 컬렉션 추가 블레이드의 설정 및 확인 단추를 강조 표시하는 스크린샷](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. **컬렉션 추가** 블레이드에서 새로운 컬렉션에 대한 ID를 입력합니다. 이름의 유효성이 검사되면 ID 상자에 녹색 확인 표시가 나타납니다.

6. 새 컬렉션에 대한 가격 책정 계층을 선택합니다. 만드는 각 컬렉션은 청구 가능 엔터티입니다. 사용 가능한 성능 수준에 대한 자세한 내용은 [DocumentDB의 성능 수준](documentdb-performance-levels.md)을 참조하세요.

7. 다음 **인덱싱 정책** 중 하나를 선택합니다.

	- **기본값** 이 정책은 문자열에 대해 같음 쿼리를 수행하고 숫자에 대해 ORDER BY, 범위 및 같음 쿼리를 사용할 때 가장 좋습니다. 이 정책에는 **범위**보다 더 낮은 인덱스 저장소 오버헤드가 있습니다.
	- **해시** 이 정책은 숫자 및 문자열 모두에 대해 같음 쿼리를 수행할 때 가장 좋습니다. 이 정책에는 가장 낮은 인덱스 저장소 오버헤드가 있습니다.
	- **범위** 이 정책은 숫자 및 문자열 모두의 범위 및 동등 쿼리에 ORDER BY를 사용할때 가장 좋습니다. 이 정책에는 **기본값** 또는 **해시**보다 더 높은 인덱스 저장소 오버헤드가 있습니다.

	인덱싱 정책에 대한 자세한 내용은 [DocumentDB 인덱싱 정책](documentdb-indexing-policies.md)을 참조하세요.

8. 화면 맨 아래의 **확인**을 클릭하여 새 컬렉션을 만듭니다.


9. 이제 **데이터베이스** 블레이드의 **컬렉션** 렌즈에 새 컬렉션이 표시됩니다.
 
	![데이터베이스 블레이드의 새 컬렉션 스크린샷](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## DocumentDB 컬렉션을 만드는 다른 방법

포털에서 컬렉션을 만들지 않아도 됩니다. [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 컬렉션을 만들 수도 있습니다. DocumentDB .NET SDK를 사용하여 컬렉션을 만드는 방법을 보여 주는 C# 코드 샘플에 대해서는 [GitHub.com](https://github.com)의 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 리포지토리에 있는 CollectionManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) 파일을 참조하세요.

## 다음 단계

이제 컬렉션이 있으므로 다음 단계는 컬렉션에 문서를 추가하거나 문서를 가져오는 것입니다. 컬렉션에 문서를 추가하는 경우 다음과 같은 몇 가지 옵션이 있습니다.

- 포털의 문서 탐색기를 사용하여 [문서를 추가](../documentdb-view-json-document-explorer.md)할 수 있습니다.
- DocumentDB 데이터 마이그레이션 도구를 사용하여 [문서 및 데이터를 가져올](documentdb-import-data.md) 수 있습니다. 이 도구를 통해 SQL Server, MongoDB, Azure 테이블 저장소 및 다른 DocumentDB 컬렉션의 데이터와 JSON 및 CSV 파일을 가져올 수 있습니다. 
- 또는 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서를 추가할 수도 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. [GitHub.com](https://github.com)의 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 리포지토리에 있는 DocumentManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) 파일은 DocumentDB .NET SDK를 사용하여 문서에 대한 CRUD 작업을 설명합니다.

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

<!---HONumber=AcomDC_1203_2015-->