---
title: Azure Cosmos DB에서 변경 피드 지원 사용 | Microsoft Docs
description: Azure Cosmos DB의 변경 피드 지원을 사용하여 문서에서 변경 내용을 추적하고 트리거와 마찬가지로 이벤트 기반 처리를 수행하고 캐시 및 분석 시스템을 최신 상태로 유지합니다.
keywords: 변경 피드
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8475c79782730e989f9590566c31ccd50af9f144
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302049"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB에서 변경 피드 지원 사용

[Azure Cosmos DB](../cosmos-db/introduction.md)는 IoT, 게이밍, 소매, 운영 로깅 응용 프로그램에 적합한 빠르고 유연한 전역 복제 데이터베이스입니다. 이러한 응용 프로그램에서 일반적인 설계 패턴은 데이터 변경 사항을 사용하여 추가 작업을 시작하는 것입니다. 이러한 추가 작업의 예를 들면 다음과 같습니다. 

* 문서가 삽입 또는 수정될 경우 알림 또는 API에 대한 호출 트리거링.
* IoT에 대한 스트림 처리 또는 분석 수행.
* 캐시, 검색 엔진 또는 데이터 웨어하우스와 동기화하거나 데이터를 콜드 스토리지로 아카이브하여 추가 데이터 이동.

Azure Cosmos DB의 **변경 피드 지원**을 사용하면 다음 그림과 같이 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

![Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Azure Cosmos DB의 모든 데이터 모델 및 컨테이너에 대해 변경 피드 지원이 제공됩니다. 하지만 변경 피드는 SQL 클라이언트를 사용하여 읽고 항목을 JSON 형식으로 직렬화합니다. MongoDB 클라이언트는 JSON 형식으로 인해 BSON 형식 문서와 JSON 형식의 변경 피드 간 불일치가 나타납니다.

다음 비디오에서는 Azure Cosmos DB 프로그램 관리자인 Andrew Liu가 Azure Cosmos DB 변경 피드 작동 방법을 설명합니다.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>변경 피드의 작동 방식

Azure Cosmos DB의 변경 피드 지원은 모든 변경 사항에 대해 Azure Cosmos DB 컬렉션을 수신하여 작동합니다. 그런 다음 변경된 문서가 수정된 순서로 정렬된 목록이 출력됩니다. 변경 사항은 지속적이며, 비동기적 및 증분적으로 처리할 수 있고 출력을 하나 이상의 소비자 사이에 분산하여 병렬 처리가 가능합니다. 

변경 피드는 이 문서의 뒷부분에서 설명하는 대로 세 가지 방식으로 읽을 수 있습니다.

*   [Azure Functions 사용](#azure-functions)
*   [Azure Cosmos DB SDK 사용](#sql-sdk)
*   [Azure Cosmos DB 변경 피드 프로세서 라이브러리 사용](#change-feed-processor)

변경 피드는 문서 컬렉션 내에서 각 파티션 키 범위에 대해 사용할 수 있으며 따라서 다음 그림과 같이 한 명 이상의 소비자에게 배포하여 병렬 처리할 수 있습니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/changefeedvisual.png)

추가 정보:
* 변경 피드는 기본적으로 모든 계정에 사용됩니다.
* 쓰기 지역 또는 [읽기 지역](distribute-data-globally.md)에서 [프로비전된 처리량](request-units.md)을 사용하여 Azure Cosmos DB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다.
* 변경 피드는 컬렉션 내의 문서에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 문서 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](time-to-live.md)을 통해 문서에 대한 제한 만료 기간을 설정할 수 있습니다. 예를 들어, 24시간으로 설정하고 해당 속성의 값을 사용하여 삭제를 캡처합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다.
* 각 문서에 대한 변경 내용이 한 번 변경 피드에 즉시 표시되고 클라이언트는 해당 검사점 논리를 관리합니다. 변경 피드 프로세서 라이브러리는 자동 검사점 및 “최소 한 번” 의미 체계를 제공합니다.
* 지정된 문서에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.
* 변경 피드는 각 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.
* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.
* 변경 내용은 파티션 키 범위에서 사용할 수 있습니다. 이 기능을 사용하면 대규모 컬렉션의 변경 내용을 여러 소비자/서버에 의해 병렬로 처리할 수 있습니다.
* 응용 프로그램은 동일한 컬렉션에서 동시에 여러 변경 피드를 요청할 수 있습니다.
* ChangeFeedOptions.StartTime을 사용하여 최초 시작 지점을 제공할 수 있습니다. 예를 들어 이렇게 주어진 클록 시간에 해당하는 연속 토큰을 찾을 수 있습니다. ContinuationToken을 지정할 경우 이것이 StartTime 및 StartFromBeginning 값보다 우선합니다. ChangeFeedOptions.StartTime의 정확도는 5초입니다. 

## <a name="use-cases-and-scenarios"></a>사용 사례 및 시나리오

변경 피드를 사용하면 많은 양의 쓰기가 포함된 큰 데이터 집합을 효율적으로 처리할 수 있고 변경된 내용을 식별하는 전체 데이터 집합을 쿼리하는 대안을 제공합니다. 

예를 들어, 변경 피드를 사용하여 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.
* 응용 프로그램 수준 데이터 계층 및 보관을 구현합니다. 즉, Azure Cosmos DB에 "핫 데이터"를 저장하고 "콜드 데이터"를 [Azure Blob Storage](../storage/common/storage-introduction.md) 또는 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)로 내보냅니다.
* 다른 파티션 구성표를 사용하여 다른 Azure Cosmos DB 계정에 대한 중단 시간이 없는 마이그레이션을 수행합니다.
* Azure Cosmos DB를 사용하여 [Azure의 람다 파이프라인](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)을 구현합니다. Azure Cosmos DB는 수집 및 쿼리를 모두 처리할 수 있는 확장성이 뛰어난 데이터베이스 솔루션을 제공하고 TCO가 낮은 람다 아키텍처를 구현합니다. 
* 장치, 센서, 인프라 및 응용 프로그램에서 이벤트 데이터를 수신하고 저장하며 [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) 또는 [Apache Spark](../hdinsight/spark/apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다. 

다음 그림은 Azure Cosmos DB를 사용하여 수집과 쿼리를 모두 수행하는 lambda 파이프라인이 어떻게 변경 피드 지원을 사용할 수 있는가를 보여줍니다. 

![수집 및 쿼리를 위한 Azure Cosmos DB 기반 lambda 파이프라인](./media/change-feed/lambda.png)

또한 [서버를 사용하지 않는](http://azure.com/serverless) 웹 및 모바일 앱 내에서는 [Azure Functions](#azure-functions)를 사용하여 해당 장치에 푸시 알림을 보내는 등의 특정 작업을 트리거하기 위해 고객의 프로필, 기본 설정 또는 위치에 대한 변경 내용과 같은 이벤트를 추적할 수 있습니다. 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Azure Functions 사용 

Azure Functions를 사용하는 경우 Azure Cosmos DB 변경 피드에 연결하는 가장 간단한 방법은 사용자의 Azure Functions 앱에 Azure Cosmos DB 트리거를 추가하는 것입니다. Azure Functions 앱에 Azure Cosmos DB 트리거를 만들 경우 연결할 Azure Cosmos DB 컬렉션을 선택합니다. 컬렉션이 변경될 때마다 함수가 트리거됩니다. 

트리거는 Azure Functions 포털에서, Azure Cosmos DB 포털에서 또는 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 [Azure Cosmos DB: Azure Functions를 통한, 서버를 사용하지 않는 데이터베이스 컴퓨팅](serverless-computing-database.md)을 참조하세요.

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>SDK 사용

Azure Cosmos DB의 [SQL SDK](sql-api-sdk-dotnet.md)는 변경 피드를 읽고 관리할 수 있는 모든 기능을 제공합니다. 하지만 많은 기능에는 많은 책임도 따릅니다. 검사점을 관리하고 문서 시퀀스 번호를 처리하거나 파티션 키를 자세히 관리하려는 경우 SDK를 사용하는 것이 적절한 방식일 수 있습니다.

이 섹션은 SQL SDK를 사용하여 변경 피드를 사용하는 방법을 안내합니다.

1. 가장 먼저 appconfig의 다음 리소스를 읽습니다. 끝점 및 인증 키 검색에 대한 소개는 [연결 문자열 업데이트](create-sql-api-dotnet.md#update-your-connection-string)에서 확인할 수 있습니다.

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. 다음과 같은 방법으로 클라이언트를 계정을 만듭니다.

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. 파티션 키 범위를 가져옵니다.

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. 모든 파티션 키 범위에 대해 ExecuteNextAsync를 호출합니다.

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> `ChangeFeedOptions.PartitionKeyRangeId` 대신, `ChangeFeedOptions.PartitionKey`를 사용하여 변경 피드를 가져올 단일 파티션 키를 지정합니다. 예: `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`
> 
>

복수의 reader가 있는 경우 **ChangeFeedOptions**를 사용하여 읽기 로드를 다른 스레드 또는 다른 클라이언트로 분산할 수 있습니다.

다음과 같은 몇 개의 코드만 있으면 바로 변경 피드를 읽을 수 있습니다. 이 문서에 사용된 전체 코드는 [GitHub 리포지토리](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)에서 확인할 수 있습니다.

위 4단계의 코드에서 마지막 줄에 있는 **ResponseContinuation**에는 문서의 마지막 LSN(논리적 시퀀스 번호)이 있으며, 다음에 이 시퀀스 번호 이후 새 문서를 읽을 때 이 LSN을 사용합니다. **ChangeFeedOption**의 **StartTime**을 사용하면 네트워크를 넓혀 문서를 가져올 수 있습니다. 따라서 **ResponseContinuation**이 null이지만 **StartTime**이 과거로 돌아갈 경우 **StartTime** 이후 변경된 모든 문서를 얻습니다. 단, **ResponseContinuation**에 값이 있는 경우에는 시스템에서 해당 LSN 이후의 모든 문서를 제공합니다.

따라서 검사점 배열은 각 파티션에 대해 LSN을 유지하는 것입니다. 파티션, 검사점, LSN, 시작 시간 등을 처리하지 않으려는 경우에는 변경 피드 프로세서 라이브러리를 사용하는 것이 더 간단한 방법입니다.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 사용 

[Azure Cosmos DB 변경 피드 프로세서 라이브러리](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed)는 이벤트 처리를 여러 소비자 사이에 손쉽게 배포하는 데 유용합니다. 이 라이브러리는 파티션 및 병렬 작동하는 여러 스레드 사이에서 변경 사항 읽기를 간소화합니다.

변경 피드 프로세서 라이브러리의 주요 이점은 각 파티션 및 구성 토큰을 관리하지 않아도 되며 각 컬렉션을 수동으로 폴링하지 않아도 된다는 점입니다.

이 변경 피드 프로세서 라이브러리는 파티션 및 병렬 작동하는 여러 스레드 사이에서 변경 사항 읽기를 간소화합니다.  이 라이브러리는 임대 메커니즘을 사용하여 파티션 사이에서 변경 사항 읽기를 자동으로 관리합니다. 다음 그림과 같이 변경 피드 프로세서 라이브러리를 사용하는 두 클라이언트를 시작하는 경우 두 클라이언트가 둘 사이에서 작업을 분할합니다. 계속해서 클라이언트 수를 늘리면 계속해서 클라이언트 사이에서 작업을 분할합니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/change-feed-output.png)

왼쪽 클라이언트가 가장 먼저 시작되었고 모든 파티션의 모니터링을 시작한 다음 두 번째 클라이언트가 시작되었고 첫 번째 클라이언트는 두 번째 클라이언트에 일부 임대를 해제합니다. 그림에서 볼 수 있듯이, 다른 컴퓨터와 클라이언트 사이에 작업을 배포하는 적절한 방법입니다.

서버를 사용하지 않는 Azure 함수로 동일한 컬렉션을 모니터링하고 동일한 임대를 사용하는 경우 두 함수는 프로세스 라이브러리가 파티션 처리 방식을 어떻게 결정하는가에 따라 다른 문서를 얻을 수 있습니다.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 이해

변경 피드 프로세서 라이브러리를 구현하는 4개의 주요 구성 요소는 '모니터링되는 컬렉션, 임대 컬렉션, 프로세서 호스트, 소비자'입니다. 

> [!WARNING]
> 응용 프로그램이 Azure Cosmos DB와 통신하기 위해 처리량을 예약할 때 컬렉션을 만드는 것은 가격에 영향을 미칩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 

**모니터링되는 컬렉션:** 모니터링되는 컬렉션은 변경 피드가 생성되는 데이터입니다. 모니터링되는 컬렉션에 대한 모든 삽입 및 변경 내용은 컬렉션의 변경 피드에 반영됩니다. 

**임대 컬렉션:** 임대 컬렉션은 여러 작업자 사이에서 변경 피드의 처리를 조정합니다. 임대는 개별 컬렉션을 사용하여 저장됩니다(각 파티션마다 하나의 임대). 이 임대 컬렉션은 변경 피드 프로세서를 실행하는 곳과 가장 근접한 쓰기 영역에서 다른 계정으로 저장하는 것이 유용합니다. 임대 개체에는 다음 특성이 포함됩니다. 
* Owner: 임대를 소유하는 호스트를 지정합니다.
* Continuation: 특정 파티션에 대한 변경 피드에서의 위치(연속 토큰)를 지정합니다.
* Timestamp: 마지막으로 임대가 업데이트된 시간입니다. 타임스탬프를 사용하여 임대가 만료된 것으로 간주되는지 여부를 확인할 수 있습니다. 

**프로세서 호스트:** 각 호스트는 활성 임대가 포함된 호스트의 다른 인스턴스 수에 따라, 처리할 파티션 수를 결정합니다. 
1.  호스트가 시작되면 임대를 획득하여 모든 호스트에서 워크로드를 분산합니다. 호스트는 임대가 활성 상태로 유지되도록 주기적으로 임대를 갱신합니다. 
2.  호스트는 읽기를 수행할 때마다 해당 임대에 대한 가장 최근 연속 토큰의 검사점을 설정합니다. 동시성 안전을 보장하기 위해 호스트는 각 임대 업데이트에 대한 ETag를 확인합니다. 다른 검사점 전략도 지원됩니다.  
3.  종료 시 호스트는 모든 임대를 해제하지만 나중에 저장된 검사점에서 읽기를 다시 시작할 수 있도록 연속 정보를 유지합니다. 

이 경우에 호스트 수는 파티션(임대) 수보다 클 수 없습니다.

**소비자:** 소비자나 작업자는 각 호스트에서 시작된 변경 피드 처리를 수행하는 스레드입니다. 각 프로세서 호스트에는 여러 소비자가 있을 수 있습니다. 각 소비자는 할당된 파티션에서 변경 피드를 읽고 호스트에게 변경 사항 및 만료된 임대를 알려줍니다.

변경 피드 프로세서의 이러한 4개의 요소가 상호 작용하는 방법을 이해하기 위해 다음 다이어그램의 예제를 살펴보겠습니다. 모니터링되는 컬렉션은 문서를 저장하고 파티션 키로 "city"를 사용합니다. 파란색 파티션에는 "A~E"의 "city" 필드가 있는 문서가 포함된 것을 확인할 수 있습니다. 두 호스트에는 동시에 4개의 파티션을 읽는 두 명의 소비자가 있습니다. 화살표는 소비자가 읽는 변경 피드의 특정 지점을 표시합니다. 첫 번째 파티션에서, 어두운 파란색은 변경 피드의 아직 읽지 않은 변경 내용을 나타내는 반면, 연한 파란색은 이미 읽은 변경 내용을 나타냅니다. 호스트는 임대 컬렉션을 사용하여 "continuation" 값을 저장하고, 이로써 각 소비자의 현재 읽기 위치를 추적할 수 있습니다. 

![Azure Cosmos DB 변경 피드 프로세서 호스트 사용](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 작업

변경 피드 프로세서 NuGet 패키지를 설치하기 전에 먼저 다음을 설치합니다. 

* Microsoft.Azure.DocumentDB, 최신 버전.
* Newtonsoft.Json, 최신 버전

그런 다음 [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)를 설치하고 참조로 포함합니다.

변경 피드 프로세서 라이브러리를 구현하려면 다음을 수행해야 합니다.

1. **IChangeFeedObserver**를 구현하는 **DocumentFeedObserver** 개체를 구현합니다.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. **IChangeFeedObserverFactory**를 구현하는 **DocumentFeedObserverFactory**를 구현합니다.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. *CancellationTokenSource* 및 *ChangeFeedProcessorBuilder* 정의

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. 관련 개체를 정의한 후 **ChangeFeedProcessorBuilder** 빌드 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Java를 사용하여 변경 피드를 읽을 수 있나요?

변경 피드를 읽는 데 사용되는 Java 라이브러리는 [Github 리포지토리](https://github.com/Azure/azure-documentdb-changefeedprocessor-java)에서 얻을 수 있습니다. 그러나 현재 Java 라이브러리는 .NET 라이브러리보다 몇 세대 뒤쳐진 버전입니다. 조만간 두 라이브러리가 동기화됩니다.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>응답에서 얻은 _etag, _lsn 또는 _ts를 내부 장부 관리에 사용할 수 있나요?

_etag 형식은 내부적이며 언제든지 변경될 수 있으므로 의존하면 안 됩니다(구문 분석하지 말 것).
_ts는 수정 또는 생성 타임스탬프입니다. _ts를 시간순 비교에 사용할 수 있습니다.
_lsn은 변경 피드에 대해서만 추가되는 일괄 처리 id로, 저장소의 트랜잭션 id를 나타냅니다. 여러 문서의 _lsn이 같을 수 있습니다.
한 가지 더 말씀드리자면, FeedResponse의 ETag는 문서에 보이는 _etag와 다릅니다. _etag는 동시성에 사용되는 내부 식별자로 문서의 버전을 알려주며, ETag는 피드 시퀀싱에 사용됩니다.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>변경 피드를 읽으면 추가 비용이 발생하나요?

사용한 RU에 따라 요금이 부과됩니다. 즉, Azure Cosmos DB 컬렉션으로 데이터가 들어가고 나올 때마다 RU가 사용됩니다. 임대 컬렉션에서 사용하는 RU만큼 요금이 청구됩니다.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>여러 Azure Functions가 한 컬렉션의 변경 피드를 읽을 수 있나요?

예. 여러 Azure Functions가 동일한 컬렉션의 변경 피드를 읽을 수 있습니다. 그러나 Azure Functions에 별도의 leaseCollectionPrefix가 정의되어야 합니다.

### <a name="should-the-lease-collection-be-partitioned"></a>임대 컬렉션을 분할할 수 있나요?

아니요, 임대 컬렉션을 수정할 수는 있습니다. 분할된 임대 컬렉션은 필요하지 않으며 현재는 지원되지도 않습니다.

### <a name="can-i-read-change-feed-from-spark"></a>Spark에서 변경 피드를 읽을 수 있나요?

예, 할 수 있습니다. [Azure Cosmos DB Spark 커넥터](spark-connector.md)를 참조하세요. 다음은 변경 피드를 구조적 스트림으로 처리하는 방법을 보여주는 [화면 캐스트](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s)입니다.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Azure Functions를 사용하여 변경 피드를 처리하고 10개 문서를 일괄 처리한다고 가정할 경우 7번째 문서에서 오류가 발생합니다. 마지막 세 문서가 처리되지 않는 경우 그 다음 피드에서 실패한 문서(즉, 7번째 문서)의 처리를 시작하려면 어떻게 해야 하나요?

오류를 처리하기 위해 권장되는 패턴은 try-catch 블록을 사용하여 코드를 래핑하는 것입니다. 오류를 catch하고 해당 문서를 큐(배달하지 못한 편지)에 배치한 다음, 오류를 생성한 문서를 처리하는 논리를 정의합니다. 이 방법을 사용하여 200개 문서 일괄 처리 중 문서 하나만 실패한 경우 일괄 처리 전체를 버릴 필요는 없습니다.

오류 발생 시 검사점을 시작 위치로 되돌리면 안 됩니다. 그렇지 않으면 변경 피드에서 이러한 문서를 계속 가져올 수 있습니다. 변경 피드는 문서의 마지막 최종 스냅숏을 보관하므로 문서의 이전 스냅숏이 손실될 수 있습니다. 변경 피드는 문서의 마지막 버전 하나만 보관하며, 그 사이에 다른 프로세스가 문서를 변경할 수 있습니다.

코드를 계속 수정하다 보면 배달하지 못한 편지 큐가 더 이상 발견되지 않을 것입니다.
변경 피드 시스템이 Azure Functions를 자동으로 호출하고, 검사점 등은 Azure 함수에 의해 내부적으로 유지됩니다. 검사점을 롤백하고 검사점과 관련된 모든 사항을 제어하려면 변경 피드 프로세서 SDK를 사용해야 합니다.


## <a name="next-steps"></a>다음 단계

Azure Cosmos DB와 Azure Functions를 사용하는 방법에 대한 자세한 내용은 [Azure Cosmos DB: Azure Functions를 통한 서버를 사용하지 않는 데이터베이스 컴퓨팅](serverless-computing-database.md)을 참조하세요.

변경 피드 프로세서 라이브러리 사용에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [정보 페이지](sql-api-sdk-dotnet-changefeed.md) 
* [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [위 1 ~ 6단계를 보여주는 샘플 코드](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [GitHub의 추가 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

SDK를 통해 변경 피드를 사용하는 방법을 보려면 다음 리소스를 사용하세요.

* [SDK 정보 페이지](sql-api-sdk-dotnet.md)
