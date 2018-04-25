---
title: 'Azure Cosmos DB: .NET의 SQL API를 사용하여 개발 | Microsoft Docs'
description: .NET을 사용하는 Azure Cosmos DB의 SQL API를 통해 개발하는 방법에 대해 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: a6ed74de159593003e8a18daefce2eb9a5945481
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: .NET의 SQL API를 사용하여 개발

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

이 자습서에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정을 만든 다음 [SQL .NET API](sql-api-introduction.md)를 사용하여 [파티션 키](sql-api-partition-data.md#partition-keys)가 있는 문서 데이터베이스와 컬렉션을 만드는 방법을 보여 줍니다. 컬렉션을 만들 때 파티션 키를 정의하면 데이터가 증가함에 따라 응용 프로그램이 손쉽게 확장하도록 준비할 수 있습니다.

이 자습서에서는 [SQL .NET API](sql-api-sdk-dotnet.md)를 사용하여 다음 작업을 설명합니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기
> * 파티션 키가 있는 데이터베이스 및 컬렉션 만들기
> * JSON 문서 만들기
> * 문서 업데이트
> * 분할된 컬렉션 쿼리
> * 저장 프로시저 실행
> * 문서 삭제
> * 데이터베이스 삭제

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음이 필요합니다.

* Azure Cosmos DB 계정에 대한 액세스

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  [무료 Azure 계정](https://azure.microsoft.com/free/)에 등록하여 사용자의 Azure 구독을 사용할 수도 있습니다. 이제부터 [Azure Cosmos DB 계정을 만들](create-sql-api-dotnet.md#create-a-database-account) 수 있습니다.

* Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.


> [!TIP]
> * Azure Cosmos DB 에뮬레이터를 사용하도록 선택하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설치하세요.
>
>


## <a id="SetupVS"></a>Visual Studio 솔루션 설치
1. 컴퓨터에서 **Visual Studio**를 엽니다.
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. **새 프로젝트** 대화 상자에서 **템플릿** / **Visual C#** / **콘솔 앱(.NET Framework)** 을 선택하고, 프로젝트 이름을 지정한 다음, **확인**을 클릭합니다.
   ![새 프로젝트 창의 스크린샷](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

    ![프로젝트의 마우스 오른쪽 단추 클릭 메뉴의 스크린샷](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. **NuGet** 탭에서 **찾아보기**를 클릭하고, 검색 상자에서 **documentdb**를 입력합니다.
<!---stopped here--->
6. 결과 내에서 **Microsoft.Azure.DocumentDB**를 찾아 **설치l**를 클릭합니다.
   Azure Cosmos DB 클라이언트 라이브러리의 패키지 ID는 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)입니다.
   ![Azure Cosmos DB 클라이언트 SDK를 찾기 위한 NuGet 메뉴의 스크린샷](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    솔루션 변경 내용을 검토하는 메시지가 표시되면 **확인**을 클릭합니다. 라이선스 승인에 관한 메시지가 표시되면 **동의합니다.** 를 클릭합니다.

## <a id="Connect"></a>프로젝트에 참조 추가
이 자습서의 나머지 단계에서는 프로젝트에서 Azure Cosmos DB 리소스를 만들고 업데이트하는 데 필요한 SQL API 코드 조각을 제공합니다.

먼저 응용 프로그램에 이러한 참조를 추가합니다.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>앱 연결

다음으로 응용 프로그램에 이 두 상수와 *client* 변수를 추가합니다.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

그런 다음 [Azure Portal](https://portal.azure.com)로 다시 이동하여 끝점 URL과 기본 키를 검색합니다. 끝점 URL과 기본 키는 응용 프로그램에서 연결할 위치를 식별하고 Azure Cosmos DB에서 응용 프로그램의 연결을 신뢰하는 데 필요합니다.

Azure Portal에서 Azure Cosmos DB 계정으로 이동합니다. 왼쪽 메뉴에서 **키**를 선택한 다음, **읽기 쓰기 키**를 선택합니다.

포털에서 URI를 복사하고 program.cs 파일의 `<your endpoint URL>`에 붙여넣습니다. 그런 다음 포털에서 기본 키를 복사하고 `<your primary key>`에 붙여넣습니다. 값에서 `<`과 `>`를 제거해야 합니다.

![C# 콘솔 응용 프로그램을 만들기 위해 NoSQL 자습서에서 사용하는 Azure Portal의 스크린샷 Azure Cosmos DB 계정 섹션에서 강조 표시된 키 및 키 섹션에서 강조 표시된 URI와 키 값이 있는 Azure Cosmos DB 계정을 보여줍니다.](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>DocumentClient 인스턴스화

이제 **DocumentClient**의 새 인스턴스를 만듭니다.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>데이터베이스 만들기

다음으로 [SQL .NET SDK](sql-api-sdk-dotnet.md)에서 **DocumentClient** 클래스의 [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 메서드 또는 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 메서드를 사용하여 Azure Cosmos DB [데이터베이스](sql-api-resources.md#databases)를 만듭니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>파티션 키 결정

컬렉션은 문서를 저장하기 위한 컨테이너입니다. 논리적인 리소스이며 [하나 이상의 물리적 파티션](partition-data.md)에 걸쳐 있을 수 있습니다. [파티션 키](sql-api-partition-data.md)는 서버 또는 파티션 간에 데이터를 배포하는 데 사용되는 문서 내의 속성(또는 경로)입니다. 동일한 파티션 키가 있는 문서는 모두 동일한 파티션에 저장됩니다.

파티션 키를 결정하는 것은 컬렉션을 만들기 전에 수행해야 하는 중요한 결정입니다. 파티션 키는 Azure Cosmos DB에서 여러 서버 또는 파티션간에 데이터를 배포하는 데 사용할 수 있는 문서 내의 속성 (또는 경로)입니다. Cosmos DB는 파티션 키 값을 해시하고 해시된 결과를 사용하여 문서를 저장할 파티션을 결정합니다. 동일한 파티션 키가 있는 문서는 모두 동일한 파티션에 저장되며, 컬렉션이 생성되면 파티션 키를 변경할 수 없습니다.

이 자습서에서는 파티션 키를 `/deviceId`로 설정하여 단일 장치의 모든 데이터가 단일 파티션에 저장되도록 해야 합니다. 값이 많은 파티션 키를 선택하려고 합니다. 각 키는 데이터가 증가함에 따라 Cosmos DB의 부하를 분산하고, 컬렉션의 전체 처리량을 달성할 수 있도록 거의 동일한 빈도로 사용됩니다.

분할에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정하는 방법](partition-data.md)을 참조하세요.

## <a id="CreateColl"></a>컬렉션 만들기

`/deviceId` 파티션 키와 함께 **DocumentClient** 클래스의 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 메서드 또는 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 메서드를 사용하여 [컬렉션](sql-api-resources.md#collections)을 만들어 보겠습니다. 컬렉션은 JSON 문서 및 관련된 모든 JavaScript 응용 프로그램 논리의 컨테이너입니다.

> [!WARNING]
> 응용 프로그램이 Azure Cosmos DB와 통신하기 위해 처리량을 예약할 때 컬렉션을 만드는 것은 가격에 영향을 미칩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
>
>

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

이 메서드에서 Azure Cosmos DB에 REST API 호출을 요청하고, 서비스는 요청된 처리량에 따라 파티션 수를 프로비전합니다. SDK 또는 [Azure Portal](set-throughput.md)을 사용하여 성능 요구 사항이 진화함에 따라 컬렉션의 처리량을 변경할 수 있습니다.

## <a id="CreateDoc"></a>JSON 문서 만들기
Azure Cosmos DB에 일부 JSON 문서를 삽입해 보겠습니다. **DocumentClient** 클래스의 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 메서드를 사용하여 [문서](sql-api-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이 샘플 클래스에는 장치 읽기와 컬렉션에 새 장치 읽기를 삽입하기 위한 CreateDocumentAsync 호출이 포함되어 있습니다.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>데이터 읽기

ReadDocumentAsync 메서드를 사용하여 파티션 키와 ID별로 해당 문서를 읽어 보겠습니다. 읽기에 PartitionKey 값(REST API의 `x-ms-documentdb-partitionkey` 요청 헤더에 해당)이 포함됩니다.

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>데이터 업데이트

이제 ReplaceDocumentAsync 메서드를 사용하여 일부 데이터를 업데이트해 보겠습니다.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>데이터 삭제

이제 DeleteDocumentAsync 메서드를 사용하여 파티션 키와 ID별로 문서를 삭제해 보겠습니다.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>분할된 컬렉션 쿼리

분할된 컬렉션에서 데이터를 쿼리하면 Azure Cosmos DB에서 필터에 지정된 파티션 키 값(있는 경우)에 해당하는 파티션으로 쿼리를 자동으로 라우팅합니다. 예를 들어 이 쿼리는 파티션 키 "XMS-0001"이 포함된 파티션으로만 라우팅됩니다.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

다음 쿼리는 파티션 키(DeviceId)에 대한 필터가 없으므로 파티션의 인덱스에 대해 실행되는 모든 파티션으로 팬아웃됩니다. SDK가 파티션에 걸쳐 쿼리를 실행하도록 EnableCrossPartitionQuery(REST API의`x-ms-documentdb-query-enablecrosspartition` )를 지정해야 합니다.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>병렬 쿼리 실행
Azure Cosmos DB SQL SDK 1.9.0 이상에서는 병렬 쿼리 실행 옵션을 지원합니다. 이 옵션을 사용하면 많은 수의 파티션을 연결해야 하는 경우에도 분할된 컬렉션에 대해 대기 시간이 짧은 쿼리를 수행할 수 있습니다. 예를 들어 다음 쿼리는 파티션에 걸쳐 병렬로 실행되도록 구성되어 있습니다.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

* `MaxDegreeOfParallelism`을 설정하여 컬렉션의 파티션에 대한 최대 동시 네트워크 연결 수를 나타내는 병렬 처리 수준을 제어할 수 있습니다. 이 매개 변수를 -1로 설정하는 경우 병렬 처리 수준이 SDK에서 관리됩니다. `MaxDegreeOfParallelism` 값이 지정되지 않거나 기본값인 0으로 설정된 경우 컬렉션의 파티션에 단일 네트워크 연결이 생성됩니다.
* `MaxBufferedItemCount`를 설정하여 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률 간에 균형을 유지할 수 있습니다. 이 매개 변수를 생략하거나 -1로 설정하는 경우 병렬 쿼리 실행 중에 버퍼링되는 항목의 수가 SDK에서 관리됩니다.

컬렉션에 동일한 상태를 지정할 경우, 병렬 쿼리는 직렬 실행의 경우와 동일한 순서로 결과를 반환합니다. 정렬(ORDER BY 및/또는 TOP)을 포함하는 파티션 간 쿼리를 수행할 경우 SQL SDK는 파티션에 걸쳐 병렬로 쿼리를 실행하고, 클라이언트 쪽에서 부분적으로 정렬된 결과를 병합하여 전역으로 정렬된 결과를 생성합니다.

## <a name="execute-stored-procedures"></a>저장 프로시저 실행
마지막으로 장치 ID가 동일한 문서에 대해 원자성 트랜잭션을 실행할 수 있습니다(예: 프로젝트에 다음 코드를 추가하여 장치의 최신 상태 또는 집계를 단일 문서에서 유지 관리하는 경우).

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

이것으로 끝입니다! 이러한 저장 프로시저는 파티션 키를 사용하여 파티션 간에 데이터 분포의 크기를 효율적으로 조정하는 Azure Cosmos DB 응용 프로그램의 주요 구성 요소입니다.  

## <a name="clean-up-resources"></a>리소스 정리

이 응용 프로그램을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 자습서에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 고유한 이름을 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기
> * 파티션 키가 있는 데이터베이스 및 컬렉션 만들기
> * JSON 문서 만들기
> * 문서 업데이트
> * 분할된 컬렉션 쿼리
> * 저장 프로시저 실행
> * 문서 삭제
> * 데이터베이스 삭제

이제 다음 자습서로 진행하여 추가 데이터를 Cosmos DB 계정으로 가져올 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)
