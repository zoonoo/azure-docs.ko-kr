---
title: 'Azure 코스모스 DB: 대량 실행기 .NET API, SDK & 리소스'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB bulk executor .NET SDK의 각 버전 간 변경 내용을 포함하여 bulk executor .NET API 및 SDK에 대한 모든 것을 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169409"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor 라이브러리: 다운로드 정보 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [나머지](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행기 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - 자바](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **설명**| .Net 대량 실행기 라이브러리를 사용하면 클라이언트 응용 프로그램이 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. 이 라이브러리는 대량 가져오기, 대량 업데이트 및 BulkDelete 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. BulkUpdate 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다. BulkDelete 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 삭제할 수 있습니다.|
|**SDK 다운로드**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub의 대량 실행기 라이브러리**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 문서**|[.NET API 참조 문서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**시작**|[대량 실행기 라이브러리 .NET SDK 시작](bulk-executor-dot-net.md)|
| **현재 지원되는 프레임워크**| Microsoft .NET Framework 4.5.2, 4.6.1 및 .NET Standard 2.0 |

> [!NOTE]
> 대량 실행기를 사용하는 경우 SDK에 대량 실행자가 내장된 [.NET SDK의](tutorial-sql-api-dotnet-bulk-import.md)최신 버전 3.x를 참조하십시오. 

## <a name="release-notes"></a>릴리스 정보

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1 미리 보기

* 대량 삭제응답에서 TotalElapsedTime을 수정하여 재시도를 포함한 총 시간을 정확하게 측정했습니다.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0 미리 보기

* SDK 종속성을 >= 2.5.1로 변경했습니다.

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-미리 보기2

* 정점 및 가장자리에 ttl을 허용하는 그래프 벌크 실행기 지원 추가

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-미리 보기2

* 게이트웨이 모드에서 실행할 때 Azure Cosmos DB의 탄력적 크기 조정 중에 예외가 발생하는 문제를 수정했습니다. 이 수정 프로그램은 기능적으로 1.4.1 릴리스와 동일합니다.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-미리 보기2

* 파티션 키를 수락하기 위해 SQL API 계정에 대한 BulkDelete 지원을 추가했으며, 문서 ID를 삭제합니다. 이 변경으로 인해 기능적으로 1.4.0 릴리스와 동일합니다.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2.0을 지원하는 MongoBulkExecutor가 포함되어 있습니다. 이 기능은 1.3.0 릴리스와 기능적으로 동일하며, 대상 프레임워크로 .NET Standard 2.0 지원이 추가되었습니다.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* .NET 표준 2.0을 지원되는 대상 프레임워크 중 하나로 추가하여 .NET Core 응용 프로그램에서 대량 실행기 라이브러리를 작동하도록 했습니다.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* 패딩을 추가하고 경우에 따라 최대 문서 크기 제한을 초과하여 예기치 않게 문서 크기를 늘리는 MongoBulkExecutor에서 문제가 해결되었습니다.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* 컬렉션에 파티션 키 경로를 중첩했을 때 BulkDeleteAsync 문제가 해결되었습니다.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor는 이제 IDisposable을 구현하고 사용 후 폐기 될 것으로 예상된다.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* SDK 버전에서 잠금이 제거되었습니다. 패키지는 이제 SDK >= 2.5.1에 따라 달라집니다.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* 숫자 값을 가진 POCO 개체 목록으로 BulkImport를 호출할 때 식별자를 수정했습니다.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* 대량 삭제응답에서 TotalElapsedTime을 수정하여 재시도를 포함한 총 시간을 정확하게 측정했습니다.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* 특정 시나리오에서 높은 CPU 소비를 수정했습니다.
* 추적은 이제 추적 소스를 사용합니다. 사용자는 소스에 대한 `BulkExecutorTrace` 리스너를 정의할 수 있습니다.
* 2Mb 크기 에 가까운 문서를 보낼 때 잠금을 일으킬 수 있는 드문 시나리오를 수정했습니다.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Azure Cosmos DB .NET SDK(2.4.0)의 최신 버전을 사용하도록 대량 실행기를 업데이트했습니다.

### <a name="150"></a><a name="1.5.0"/>1.5.0

* 정점 및 가장자리에 ttl을 허용하는 그래프 벌크 실행기 지원 추가

### <a name="141"></a><a name="1.4.1"/>1.4.1

* 게이트웨이 모드에서 실행할 때 Azure Cosmos DB의 탄력적 크기 조정 중에 예외가 발생하는 문제를 수정했습니다.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* 파티션 키를 수락하기 위해 SQL API 계정에 대한 BulkDelete 지원을 추가했으며, 문서 ID를 삭제합니다.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* 대량 실행기에서 사용하는 사용자 에이전트에서 서식 문제가 발생했던 문제를 수정했습니다.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* 저장소가 예외를 주지 않고 현재 용량을 초과할 때 Cosmos 컨테이너의 탄력적 크기 조정에 투명하게 적응하도록 대량 실행기 가져오기 및 업데이트 API를 개선했습니다.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK 종속성이 버전 2.1.3으로 향상되었습니다.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* 고정 된 컬렉션으로 가져오는 동안 대량 실행자가 JSRT 오류를 발생시키는 문제를 수정했습니다.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API 계정용 BulkDelete 작업에 대한 지원이 추가되었습니다.
* Azure Cosmos DB의 MongoDB API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.
* DocumentDB.NET SDK 종속성이 버전 2.0.0으로 향상되었습니다. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Azure Cosmos DB Gremlin API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Azure Cosmos DB SQL API 계정용 BulkImport 작업에 대한 사소한 버그가 수정되었습니다.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API 계정용 BulkImport 및 BulkUpdate 작업에 대한 지원이 추가되었습니다.

## <a name="next-steps"></a>다음 단계

대량 실행기 Java 라이브러리에 대해 자세히 알아보려면 다음 문서를 참조하십시오.

[자바 대량 실행기 라이브러리 SDK 및 릴리스 정보](sql-api-sdk-bulk-executor-java.md)
