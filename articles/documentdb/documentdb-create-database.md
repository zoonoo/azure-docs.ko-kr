<properties 
	pageTitle="NoSQL DocumentDB 데이터베이스 만들기 | Microsoft Azure" 
	description="Azure DocumentDB용 온라인 서비스 포털, JSON용 NoSQL 문서 데이터베이스를 사용하여 관리되는 데이터베이스를 만드는 방법을 알아봅니다. 지금 무료 평가판을 사용해보세요." 
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
	ms.topic="get-started-article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Azure Preview 포털을 사용하여 DocumentDB 데이터베이스 만들기

Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), 데이터베이스, 컬렉션 및 문서가 있어야 합니다. 이 항목에서는 Microsoft Azure Preview 포털에서 DocumentDB 데이터베이스를 만드는 방법을 설명합니다.

Preview 포털에서 데이터베이스를 만들지 않아도 됩니다. [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 데이터베이스를 만들 수도 있습니다. DocumentDB .NET SDK를 사용하여 데이터베이스를 만드는 방법을 보여주는 C# 코드 샘플에 대해서는 [GitHub.com](https://github.com)의 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net)에 있는 DatabaseManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) 파일을 참조하십시오.

1.  [Azure Preview 포털](https://portal.azure.com/)에서 **모두 찾아보기**를 클릭합니다.

2.  **찾아보기** 블레이드에서 **DocumentDB 계정**을 클릭합니다.

3.  **DocumentDB 계정** 블레이드에서 DocumentDB 데이터베이스를 추가할 계정을 선택합니다. 나열된 계정이 없는 경우 [DocumentDB 계정을 만들](documentdb-create-account.md)어야 합니다.
    
    ![찾아보기 단추, 찾아보기 블레이드의 DocumentDB 계정 및 DocumentDB 계정 블레이드의 DocumentDB 계정이 강조 표시된 스크린샷](./media/documentdb-create-database/docdb-database-creation-1-3.png)

4. **DocumentDB 계정** 블레이드에서 **데이터베이스 추가**를 클릭합니다.

5. **데이터베이스 추가** 블레이드에서 새 데이터베이스의 ID를 입력합니다. 이름의 유효성이 검사되면 ID 상자에 녹색 확인 표시가 나타납니다.

6. 화면 맨 아래의 **확인**을 클릭하여 새 데이터베이스를 만듭니다.

	![DocumentDB 계정 블레이드의 데이터베이스 추가 단추, 데이터베이스 추가 블레이드의 ID 상자 및 확인 단추가 강조 표시된 스크린샷](./media/documentdb-create-database/docdb-database-creation-4-6.png)

8. 이제 **DocumentDB 계정** 블레이드의 **데이터베이스** 렌즈에 새 데이터베이스가 표시됩니다.
 
	![DocumentDB 계정 블레이드의 새 데이터베이스 스크린샷](./media/documentdb-create-database/docdb-database-creation-7.png)

## 다음 단계

이제 DocumentDB 데이터베이스가 있으므로 다음 단계는 [컬렉션을 만드는](documentdb-create-collection.md) 것입니다.

컬렉션을 만든 후에는 Preview 포털의 문서 탐색기를 사용하여 [문서를 추가](../documentdb-view-json-document-explorer.md)하고 DocumentDB 데이터 마이그레이션 도구를 사용하여 컬렉션으로 [문서를 가져오거나](documentdb-import-data.md) [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 하나를 사용하여 CRUD 작업을 수행할 수 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. 문서 만들기, 제거, 업데이트 및 삭제 방법을 보여주는 .NET 코드 샘플은 GitHub.com의 azure-documentdb-net 리포지토리에 있는 DocumentManagement 프로젝트 내의 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs)를 참조하십시오.

컬렉션에 문서를 추가한 후에 [DocumentDB SQL](documentdb-sql-query.md)에서 Preview 포털에 있는 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서에 대해 [쿼리를 실행](documentdb-sql-query.md#executing-queries)할 수 있습니다.

<!---HONumber=July15_HO5-->