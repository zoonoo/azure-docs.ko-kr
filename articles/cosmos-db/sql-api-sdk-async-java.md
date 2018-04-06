---
title: 'Azure Cosmos DB: SQL 비동기 Java API, SDK 및 리소스 | Microsoft Docs'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL 비동기 Java SDK의 각 버전 간 변경 내용을 포함하여 SQL 비동기 Java API 및 SDK에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: 07680340ca0f4f54c2ee8645f1374b6e9d08f393
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK for SQL API: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

SQL API 비동기 Java SDK는 [Netty 라이브러리](http://netty.io/)를 지원하고 비동기 작업을 제공하기 때문에 SQL API Java SDK와는 다릅니다. 기존 [SQL API Java SDK](sql-api-sdk-java.md)는 비동기 작업을 지원하지 않습니다. 

<table>

<tr><td>**SDK 다운로드**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API 설명서**</td><td>[Java API 참조 설명서](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**SDK에 참여**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**시작**</td><td>[비동기 Java SDK 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**코드 샘플**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**성능 팁**</td><td>[Github 추가 정보](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**지원되는 최소 런타임**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 게이트웨이 모드에서 [Netty 라이브러리](http://netty.io/)를 사용하여 비차단 IO에 대한 종단 간 지원을 제공하는 GA SDK입니다. 

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.0.0](#1.0.0) |2018년 2월 27일|--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

