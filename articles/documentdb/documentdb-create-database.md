---
title: "DocumentDB에서 데이터베이스를 만드는 방법 | Microsoft Docs"
description: "획기적인 속도, 세계적인 규모의 NoSQL 데이터베이스인, Azure DocumentDB용 온라인 서비스 포털을 사용하여 데이터베이스를 만드는 방법을 알아봅니다."
keywords: "데이터베이스를 만드는 방법"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 2503db56-744f-44b5-bf6a-0be821d023ea
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5d93b218a61b325b6c7bd02a160bd2cae844341


---
# <a name="how-to-create-a-database-for-documentdb-using-the-azure-portal"></a>Azure 포털을 사용하여 DocumentDB용 데이터베이스를 만드는 방법
Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), 데이터베이스, 컬렉션 및 문서가 있어야 합니다. 이제 Azure Portal에서 컬렉션을 만들 때 DocumentDB 데이터베이스를 동시에 만듭니다. 

포털에서 DocumentDB 데이터베이스와 컬렉션을 만들려면 [Azure Portal을 사용하여 DocumentDB 컬렉션을 만드는 방법](documentdb-create-collection.md)을 참조하세요.

## <a name="other-ways-to-create-a-documentdb-database"></a>DocumentDB 데이터베이스를 만드는 다른 방법
포털을 사용하여 데이터베이스를 만들지 않아도 됩니다. 또한 [DocumentDB SDK](documentdb-sdk-dotnet.md) 또는 [REST API](https://msdn.microsoft.com/library/mt489072.aspx)를 사용하여 데이터베이스를 만들 수 있습니다. .NET SDK를 사용한 데이터베이스 작업에 대한 자세한 내용은 [.NET 데이터베이스 예제](documentdb-dotnet-samples.md#database-examples)를 참조하세요. Node.js SDK를 사용한 데이터베이스 작업에 대한 자세한 내용은 [Node.js 데이터베이스 예제](documentdb-nodejs-samples.md#database-examples)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
데이터베이스와 컬렉션을 만든 후에 포털의 문서 탐색기를 사용하여 [JSON 문서를 추가](documentdb-view-json-document-explorer.md)하고, DocumentDB 데이터 마이그레이션 도구를 사용하여 컬렉션으로 [문서를 가져오거나](documentdb-import-data.md), [DocumentDB SDK](documentdb-sdk-dotnet.md) 하나를 사용하여 CRUD 작업을 수행할 수 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. 문서 만들기, 제거, 업데이트 및 삭제 방법을 보여주는 .NET 코드 샘플의 경우, [.NET 문서 예제](documentdb-dotnet-samples.md#document-examples)를 참조하세요. Node.js SDK를 사용한 문서 작업에 대한 자세한 내용은 [Node.js 문서 예제](documentdb-nodejs-samples.md#document-examples)를 참조하세요. 

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-sql-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다. 




<!--HONumber=Nov16_HO3-->


