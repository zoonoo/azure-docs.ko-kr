---
title: "Azure Cosmos DB: DocumentDB API 문서 | Microsoft Docs"
description: "Azure Cosmos DB에서 DocumentDB API를 사용하여 문서 데이터베이스를 만드는 데 특정된 모든 문서 목록입니다."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: ce24e445177b7e2e499309f016478030efa6bfe8
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-documentdb-api-documentation"></a>Azure Cosmos DB: DocumentDB API 설명서

이 문서는 DocumentDB API에 특정된 모든 Azure Cosmos DB 콘텐츠에 대한 링크를 제공합니다.

이러한 문서는 Graph API, Table API 또는 MongoDB API에는 적용되지 않습니다. 

## <a name="introduction-and-concepts"></a>소개 및 개념

Azure Cosmos DB용 DocumentDB API에 대한 학습을 시작하기 위해 일거야 하는 항목과 리소스입니다.

- [DocumentDB API 소개](documentdb-introduction.md)
- [데이터베이스 리소스 모델](documentdb-resources.md)
- 웹 사이트: [쿼리 실습](https://www.documentdb.com/sql/demo)
- 치트 시트: [SQL 문법](documentdb-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>빠른 시작

빠른 시작 항목은 Azure Cosmos DB를 사용하여 응용 프로그램을 만드는 가장 빠른 방법입니다. 각 빠른 시작에서는 UI 기반 Azure Portal 및 즐겨 찾는 코딩 언어를 사용하여 Azure Cosmos DB를 사용하여 데이터베이스 솔루션을 만드는 방법에 대해 설명합니다. 각 빠른 시작에 사용할 수 있는 GitHub 영역에서 복제 가능한 웹앱입니다. 

- [.NET + Azure Portal + 웹앱](create-documentdb-dotnet.md)
- [Java + Azure Portal + 웹앱](create-documentdb-java.md)
- [Node.js + Azure Portal + 웹앱](create-documentdb-nodejs.md)
- [Python + Azure Portal + 웹앱](create-documentdb-python.md)
- [Xamarin + .NET + Azure Portal + 웹앱](create-documentdb-xamarin-dotnet.md)

## <a name="tutorials"></a>자습서

이 자습서는 빠른 시작보다 수준이 높습니다. 자습서에서는 처음부터 앱을 빌드하고 코드를 복사하여 앱에 붙여넣습니다. 또한 데이터를 가져오고, 데이터를 쿼리하고, 데이터베이스를 전역적으로 배포하는 방법을 설명합니다.

### <a name="create-a-web-app"></a>웹앱 만들기

- [.NET](documentdb-dotnet-application.md)
- [Node.JS](documentdb-nodejs-application.md) 
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

### <a name="create-a-console-app"></a>콘솔 앱 만들기

- [.NET](documentdb-get-started.md)
- [.NET Core](documentdb-dotnetcore-get-started.md) 
- [Java](documentdb-java-get-started.md) 
- [Node.JS](documentdb-nodejs-get-started.md) 
- [C++](documentdb-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>모바일 앱 만들기

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>데이터 작업

- [데이터 가져오기](import-data.md)
- [쿼리](tutorial-query-documentdb.md)
- [전세계로 데이터 배포](tutorial-global-distribution-documentdb.md)

### <a name="work-with-azure-functions"></a>Azure Functions와 통합

- [Azure Cosmos DB 트리거 만들기](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [입력 및 출력 바인딩에서 Azure Cosmos DB 사용](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>개발자 가이드

- [SQL 쿼리](documentdb-sql-query.md)
- [분할](documentdb-partition-data.md)
- [서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF](programming.md)
- [성능 테스트](performance-testing.md)
- [성능 팁](performance-tips.md)
- [다중 마스터 설치](multi-region-writers.md)
- [DateTimes](working-with-dates.md)
- [문서 데이터 모델링](modeling-data.md) 

## <a name="sdks"></a>SDK

Azure Cosmos DB는 클라이언트 쪽 응용 프로그램을 개발할 수 있도록 다양한 SDK를 제공합니다.

- [Java](documentdb-sdk-java.md)
- [.NET](documentdb-sdk-dotnet.md)
- [.NET 변경 피드](documentdb-sdk-dotnet-changefeed.md)
- [.NET Core](documentdb-sdk-dotnet-core.md)
- [Node.JS](documentdb-sdk-node.md)
- [Python](documentdb-sdk-python.md)

## <a name="reference"></a>참조

- [REST (영문)](/rest/api/documentdb/)
- [REST 리소스 공급자](/rest/api/documentdbresourceprovider/)
- [SQL 쿼리 참조](documentdb-sql-query-reference.md)
- [Azure Functions 참조](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>샘플

이러한 샘플 페이지는 가장 공통적인 DocumentDB API 작업에 샘플 코드와 API 참조 콘텐츠에 대한 링크를 제공합니다.

- [.NET](documentdb-dotnet-samples.md)
- [Node.JS](documentdb-nodejs-samples.md)
- [Python](documentdb-python-samples.md) 