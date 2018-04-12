---
title: 'Azure Cosmos DB: SQL API 문서 | Microsoft Docs'
description: Azure Cosmos DB에서 SQL API를 사용하여 문서 데이터베이스를 만드는 데 특정된 모든 문서 목록입니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: sngun
ms.openlocfilehash: 5e6f277a3c020334ba26a0b1a2681cbbdd672f45
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-sql-api-documentation"></a>Azure Cosmos DB: SQL API 설명서

이 문서는 SQL API에 특정된 모든 Azure Cosmos DB 콘텐츠에 대한 링크를 제공합니다.

이러한 문서는 Graph API, Table API 또는 MongoDB API에는 적용되지 않습니다. 

## <a name="introduction-and-concepts"></a>소개 및 개념

Azure Cosmos DB에서 SQL API에 대해 학습하기 시작할 때 읽어야 하는 항목과 리소스입니다.

- [SQL API 소개](sql-api-introduction.md)
- [데이터베이스 리소스 모델](sql-api-resources.md)
- 웹 사이트: [쿼리 실습](https://www.documentdb.com/sql/demo)
- 치트 시트: [SQL 문법](query-cheat-sheet.md)

## <a name="quickstarts"></a>빠른 시작

빠른 시작 항목은 Azure Cosmos DB를 사용하여 응용 프로그램을 만드는 가장 빠른 방법입니다. 각 빠른 시작에서는 UI 기반 Azure Portal 및 즐겨 찾는 코딩 언어를 사용하여 Azure Cosmos DB를 사용하여 데이터베이스 솔루션을 만드는 방법에 대해 설명합니다. GitHub에서 복제 가능한 웹앱은 각 빠른 시작에서 사용할 수 있습니다. 

- [.NET + Azure Portal + 웹앱](create-sql-api-dotnet.md)
- [Java + Azure Portal + 웹앱](create-sql-api-java.md)
- [Node.js + Azure Portal + 웹앱](create-sql-api-nodejs.md)
- [Python + Azure Portal + 웹앱](create-sql-api-python.md)
- [Xamarin + .NET + Azure Portal + 웹앱](create-sql-api-xamarin-dotnet.md)

## <a name="tutorials"></a>자습서

이 자습서는 빠른 시작보다 수준이 높습니다. 자습서에서는 처음부터 앱을 빌드하고 코드를 복사하여 앱에 붙여넣습니다. 또한 데이터를 가져오고, 데이터를 쿼리하고, 데이터베이스를 전역적으로 배포하는 방법을 설명합니다.

### <a name="create-a-web-app"></a>웹앱 만들기

- [.NET](sql-api-dotnet-application.md)
- [Node.js](sql-api-nodejs-application.md) 
- [Java](sql-api-java-application.md)
- [Python](sql-api-python-application.md)

### <a name="create-a-console-app"></a>콘솔 앱 만들기

- [.NET](sql-api-get-started.md)
- [.NET Core](sql-api-dotnetcore-get-started.md) 
- [Java](sql-api-java-get-started.md) 
- [Node.js](sql-api-nodejs-get-started.md) 
- [C++](sql-api-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>모바일 앱 만들기

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>데이터 작업

- [데이터 가져오기](import-data.md)
- [쿼리](tutorial-query-sql-api.md)
- [전세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

### <a name="work-with-azure-functions"></a>Azure Functions와 통합

- [Azure Cosmos DB 트리거 만들기](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [입력 및 출력 바인딩에서 Azure Cosmos DB 사용](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>개발자 가이드

- [SQL 쿼리](sql-api-sql-query.md)
- [분할](sql-api-partition-data.md)
- [서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF](programming.md)
- [성능 테스트](performance-testing.md)
- [성능 팁](performance-tips.md)
- [다중 마스터 설치](multi-region-writers.md)
- [DateTimes](working-with-dates.md)
- [문서 데이터 모델링](modeling-data.md) 

## <a name="sdks"></a>SDK

Azure Cosmos DB는 클라이언트 쪽 응용 프로그램을 개발할 수 있도록 다양한 SDK를 제공합니다.

- [Java](sql-api-sdk-java.md)
- [.NET](sql-api-sdk-dotnet.md)
- [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
- [.NET Core](sql-api-sdk-dotnet-core.md)
- [Node.js](sql-api-sdk-node.md)
- [Python](sql-api-sdk-python.md)

## <a name="reference"></a>참고 자료

- [REST](/rest/api/cosmos-db/)
- [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
- [SQL 쿼리 참조](sql-api-sql-query-reference.md)
- [Azure Functions 참조](../azure-functions/functions-bindings-cosmosdb.md)

## <a name="samples"></a>샘플

이러한 샘플 페이지는 가장 공통적인 SQL API 작업에 샘플 코드와 API 참조 콘텐츠에 대한 링크를 제공합니다.

- [.NET](sql-api-dotnet-samples.md)
- [Node.js](sql-api-nodejs-samples.md)
- [Python](sql-api-python-samples.md) 
