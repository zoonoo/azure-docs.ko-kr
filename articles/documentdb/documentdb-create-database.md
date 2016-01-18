<properties 
	pageTitle="DocumentDB에서 데이터베이스를 만드는 방법 | Microsoft Azure" 
	description="JSON용 NoSQL 문서 데이터베이스인 Azure DocumentDB용 온라인 서비스 포털을 사용하여 데이터베이스를 만드는 방법을 알아봅니다. 지금 무료 평가판을 사용해보세요." 
	keywords="데이터베이스를 만드는 방법" 
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
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# DocumentDB 데이터베이스를 만드는 방법

Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), 데이터베이스, 컬렉션 및 문서가 있어야 합니다. 이 항목에서는 Microsoft Azure 포털에서 DocumentDB 데이터베이스를 만드는 방법을 설명합니다.

![데이터베이스를 만드는 방법을 보여 주는 스크린샷(찾아보기 단추, 찾아보기 블레이드의 DocumentDB 계정 및 DocumentDB 계정 블레이드의 DocumentDB 계정이 강조 표시되어 있음)](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB 계정**을 클릭합니다. 

2.  **DocumentDB 계정** 블레이드에서 DocumentDB NoSQL 데이터베이스를 추가할 계정을 선택합니다. 나열된 계정이 없는 경우 [DocumentDB 계정을 만들](documentdb-create-account.md)어야 합니다.

3. **DocumentDB 계정** 블레이드에서 **데이터베이스 추가**를 클릭합니다.

4. **데이터베이스 추가** 블레이드에서 새 데이터베이스의 ID를 입력합니다. 이름의 유효성이 검사되면 **ID** 상자에 녹색 확인 표시가 나타납니다.

5. 화면 맨 아래의 **확인**을 클릭하여 새 데이터베이스를 만듭니다.

7. 이제 **DocumentDB 계정** 블레이드의 **데이터베이스** 렌즈에 새 데이터베이스가 표시됩니다.
 
	![DocumentDB 계정 블레이드의 새 데이터베이스 스크린샷](./media/documentdb-create-database/docdb-database-creation-7.png)

## DocumentDB 데이터베이스를 만드는 다른 방법

포털을 사용하여 데이터베이스를 만들지 않아도 됩니다. 또한 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 데이터베이스를 만들 수 있습니다. DocumentDB .NET SDK를 사용하여 데이터베이스를 만드는 방법을 보여주는 C# 코드 샘플에 대해서는 [GitHub.com](https://github.com)의 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 리포지토리에 있는 DatabaseManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) 파일을 참조하세요.

## 다음 단계

이제 DocumentDB 데이터베이스를 만드는 방법을 알았으므로 다음 단계에서는 [컬렉션을 만들어야](documentdb-create-collection.md) 합니다.

컬렉션을 만든 후에 포털의 문서 탐색기를 사용하여 [JSON 문서를 추가](documentdb-view-json-document-explorer.md)하고, DocumentDB 데이터 마이그레이션 도구를 사용하여 컬렉션으로 [문서를 가져오거나](documentdb-import-data.md), [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 하나를 사용하여 CRUD 작업을 수행할 수 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. 문서 만들기, 제거, 업데이트 및 삭제 방법을 보여주는 .NET 코드 샘플은 GitHub.com의 azure-documentdb-net 리포지토리에 있는 DocumentManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs)를 참조하세요.

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

<!---HONumber=AcomDC_0107_2016-->