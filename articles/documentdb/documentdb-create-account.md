<properties
	pageTitle="DocumentDB 계정을 만드는 방법 | Microsoft Azure"
	description="Azure DocumentDB를 사용하여 NoSQL 데이터베이스를 빌드합니다. 다음 지침을 수행하여 DocumentDB 계정을 만들고 신속한 글로벌 규모의 NoSQL 데이터베이스를 빌드하기 시작합니다." 
	keywords="데이터베이스 빌드"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/25/2016"
	ms.author="mimig"/>

# Azure 포털을 사용하여 DocumentDB 계정을 만드는 방법

> [AZURE.SELECTOR]
- [Azure 포털](documentdb-create-account.md)
- [Azure CLI 및 ARM](documentdb-automation-resource-manager-cli.md)

Microsoft Azure DocumentDB를 사용하여 데이터베이스를 빌드하려면 다음을 수행해야 합니다.

- Azure 계정이 있어야 합니다. 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free)을 얻을 수 있습니다.
- DocumentDB 계정을 만듭니다.

Azure 포털, Azure Resource Manager 또는 Azure CLI(명령줄 인터페이스)를 사용하여 DocumentDB 계정을 만들 수 있습니다. 이 문서에서는 Azure 포털에서 DocumentDB 계정을 만드는 방법을 보여 줍니다. Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 계정을 만들려면 [DocumentDB 데이터베이스 만들기 자동화](documentdb-automation-resource-manager-cli.md)를 참조하세요.

DocumentDB를 처음 사용하세요? Scott Hanselman 씨가 나오는 [이](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) 4분 분량의 동영상을 보고 온라인 포털에서 가장 일반적인 작업을 완료하는 방법을 확인하세요.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## 다음 단계

이제 DocumentDB 계정을 만들었으므로 다음 단계에서는 DocumentDB 데이터베이스를 빌드합니다. 다음 중 하나를 사용하여 데이터베이스를 만들 수 있습니다.

- Azure 포털, [Azure 포털을 사용하여 DocumentDB 데이터베이스 만들기](documentdb-create-database.md)의 설명 참조.
- GitHub [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 리포지토리의 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 프로젝트에 있는 C# .NET 샘플
- 통합형 자습서: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) 또는 [Python](documentdb-python-application.md).
- [DocumentDB SDK](documentdb-sdk-dotnet.md). DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다.


데이터베이스를 만든 후에 데이터베이스에 [하나 이상의 컬렉션을 추가](documentdb-create-collection.md)한 다음, 이 컬렉션에 [문서를 추가](documentdb-view-json-document-explorer.md)해야 합니다.

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

DocumentDB에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

-	[DocumentDB 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB 리소스 모델 및 개념](documentdb-resources.md)

<!---HONumber=AcomDC_0831_2016-->