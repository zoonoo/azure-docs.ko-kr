---
title: 'Azure Cosmos DB: 대량 실행자 Java API, SDK & 리소스'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Bulk Executor Java SDK의 각 버전 간 변경 내용을 포함하여 Bulk Executor Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836447"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor 라이브러리: 다운로드 정보

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행자-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**설명**|클라이언트 애플리케이션은 Bulk Executor 라이브러리를 통해 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. Bulk Executor 라이브러리는 BulkImport 및 BulkUpdate 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. 대량 업데이트 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다.|
|**SDK 다운로드**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub의 대량 실행자 라이브러리**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 설명서**| [Java API 참조 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**시작**|[Bulk Executor 라이브러리 Java SDK 시작](bulk-executor-java.md)|
|**지원되는 최소 런타임**|[JDK (Java Development Kit) 7 이상](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>릴리스 정보

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* 문서 분석기에 대 한 수정. json에서 중첩 된 파티션 키 값을 올바르게 추출 하는 java입니다.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* 대량 삭제 작업의 기능을 추가 하 여 특정 오류를 다시 시도 하 고 다시 시도할 수 있는 사용자에 게 오류 목록을 반환 합니다.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Cosmos SDK 버전 2.4.7를 업데이트 합니다.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* ' N a m e '에 대 한 수정-' id ' 및 파티션 키 값을 계속 하려면 ' id ' 및 파티션 키 값을 업데이트 된 항목 목록에 추가 합니다.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* 시작 수준 동시성을 1로 업데이트 하 고 미니 일괄 처리에 대 한 디버그 로그를 추가 합니다.


