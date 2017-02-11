---
title: "Azure DocumentDB에서 변경 피드 지원 사용 | Microsoft Docs"
description: "Azure DocumentDB의 변경 피드 지원을 사용하여 DocumentDB 문서에서 변경 내용을 추적하고 트리거와 마찬가지로 이벤트 기반 처리를 수행하고 캐시 및 분석 시스템을 최신 상태로 유지합니다."
keywords: "변경 피드"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 12/13/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: b22e75264345bc9d155bd1abc1fdb6e978dfad04
ms.openlocfilehash: bafc50750381616ecf30c4e41090f342d82007f9


---
# <a name="working-with-the-change-feed-support-in-azure-documentdb"></a>Azure DocumentDB에서 변경 피드 지원 사용
[Azure DocumentDB](documentdb-introduction.md)는 빠르고 유연성 있는 NoSQL 데이터베이스 서비스로 읽기 및 쓰기에 대해 한 자리수 밀리초인 예측 가능한 대기 시간 동안 대량의 트랜잭션 및 운영 데이터를 저장하는 데 사용됩니다. 따라서 IoT, 게임, 소매 및 운영 로깅 응용 프로그램에 적합합니다. 이러한 응용 프로그램에서 일반적인 디자인 패턴은 DocumentDB 데이터에 대한 변경 내용을 추적하고 구체화된 뷰를 업데이트하며 실시간 분석을 수행하고 콜드 저장소에 데이터를 보관하며 이러한 변경 내용을 기반으로 특정 이벤트에 대한 알림을 트리거하는 것입니다. DocumentDB의 **변경 피드 지원**을 사용하면 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

DocumentDB에서는 변경 피드 지원을 사용하여 DocumentDB 컬렉션 내 문서의 정렬된 목록을 수정된 순서로 제공합니다. 컬렉션 내에서 데이터에 대한 수정을 수신 대기하고 다음과 같은 작업을 수행하기 위해 이 피드를 사용할 수 있습니다.

* 문서를 삽입하거나 수정하는 경우 API에 대한 호출 트리거
* 업데이트에 대한 실시간(스트림) 처리 수행
* 캐시, 검색 엔진 또는 데이터 웨어하우스와 데이터 동기화

DocumentDB의 변경 내용이 유지되면 비동기적으로 처리되고 병렬 처리를 위해 한 명 이상의 소비자에게 분산될 수 있습니다. 변경 피드에 대한 API 및 이를 사용하여 확장 가능한 실시간 응용 프로그램을 빌드하는 방법에 대해 살펴보겠습니다.

![DocumentDB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/documentdb-change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>사용 사례 및 시나리오
변경 피드를 사용하면 많은 양의 쓰기가 포함된 큰 데이터 집합을 효율적으로 처리할 수 있고 변경된 내용을 식별하는 전체 데이터 집합을 쿼리하는 대안을 제공합니다. 예를 들어, 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure DocumentDB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.
* 응용 프로그램 수준 데이터 계층 및 보관을 구현합니다. 즉, DocumentDB에 "핫 데이터"를 저장하고 "콜드 데이터"를 [Azure Blob Storage](../storage/storage-introduction.md) 또는 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)로 내보냅니다.
* [Apache Hadoop](documentdb-run-hadoop-with-hdinsight.md)를 사용하여 데이터에 대한 Batch 분석을 구현합니다.
* DocumentDB를 사용하여 [Azure의 람다 파이프라인](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)을 구현합니다. DocumentDB는 수집 및 쿼리를 모두 처리할 수 있는 확장성이 뛰어난 데이터베이스 솔루션을 제공하고 TCO가 낮은 람다 아키텍처를 구현합니다. 
* 다른 파티션 구성표를 사용하여 다른 Azure DocumentDB 계정에 대한 중단 시간이 없는 마이그레이션을 수행합니다.

**수집 및 쿼리에 Azure DocumentDB를 사용하는 람다 파이프라인:**

![수집 및 쿼리에 대한 Azure DocumentDB 기반 람다 파이프라인](./media/documentdb-change-feed/lambda.png)

DocumentDB를 사용하여 장치, 센서, 인프라 및 응용 프로그램에서 이벤트 데이터를 수신하고 저장하며 [Azure Stream Analytics](documentdb-search-indexer.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) 또는 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다. 

웹 및 모바일 앱 내에서는 [Azure Functions](../azure-functions/functions-bindings-documentdb.md) 또는 [App Services](https://azure.microsoft.com/services/app-service/)를 사용하여 해당 장치에 푸시 알림을 보내는 등의 특정 작업을 트리거하기 위해 고객의 프로필, 기본 설정 또는 위치에 대한 변경 내용과 같은 이벤트를 추적할 수 있습니다. 예를 들어, DocumentDB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

## <a name="how-change-feed-works-in-azure-documentdb"></a>Azure DocumentDB에서 변경 피드의 작동 방식
DocumentDB에서는 증분 방식으로 DocumentDB 컬렉션에 이뤄진 업데이트를 읽는 기능을 제공합니다. 이 변경 피드에는 다음과 같은 속성이 있습니다.

* 변경 내용이 DocumentDB에서 지속되면 비동기적으로 처리될 수 있습니다.
* 컬렉션 내 문서에 대한 변경 내용은 변경 피드에서 즉시 사용할 수 있습니다.
* 문서에 대한 변경 내용은 각각 변경 피드에 한 번만 표시됩니다. 지정된 문서에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.
* 변경 피드는 각 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.
* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.
* 변경 내용은 파티션 키 범위에서 사용할 수 있습니다. 이 기능을 사용하면 대규모 컬렉션의 변경 내용을 여러 소비자/서버에 의해 병렬로 처리할 수 있습니다.
* 응용 프로그램은 동일한 컬렉션에서 동시에 여러 변경 피드를 요청할 수 있습니다.

DocumentDB의 변경 피드는 모든 계정에 대해 기본적으로 활성화되고 사용자 계정에 추가 비용이 발생하지 않습니다. 쓰기 지역 또는 [읽기 지역](documentdb-distribute-data-globally.md)에서 [프로비전된 처리량](documentdb-request-units.md)을 사용하여 DocumentDB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다. 변경 피드는 컬렉션 내의 문서에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 문서 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](documentdb-time-to-live.md)을 통해 문서에 대한 제한 만료 기간을 설정할 수 있습니다. 예를 들어, 24시간으로 설정하고 해당 속성의 값을 사용하여 삭제를 캡처합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다. 변경 피드는 문서 컬렉션 내에서 각 파티션 키 범위에 대해 사용할 수 있으며 따라서 병렬 처리를 위해 한 명 이상의 소비자에게 배포할 수 있습니다. 

![DocumentDB 변경 피드의 분산 처리](./media/documentdb-change-feed/changefeedvisual.png)

다음 섹션에서는 DocumentDB REST API 및 SDK를 사용하여 변경 피드에 액세스하는 방법에 대해 설명합니다.

## <a name="working-with-the-rest-api-and-sdk"></a>REST API 및 SDK 사용
DocumentDB에서는 **컬렉션**이라는 저장소 및 처리량의 탄력적인 컨테이너를 제공합니다. 확장성 및 성능을 위해 [파티션 키](documentdb-partition-data.md)를 사용하여 컬렉션 내의 데이터를 논리적으로 그룹화합니다. DocumentDB에서는 ID(읽기/가져오기), 쿼리 및 읽기-피드(검색) 기준 조회를 포함하여 이 데이터에 액세스하기 위한 다양한 API를 제공합니다. 변경 피드는 DocumentDB의 `ReadDocumentFeed` API에 두 가지 새로운 요청 헤더를 채워서 가져올 수 있으며 파티션 키 범위에서 동시에 처리할 수 있습니다.

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
ReadDocumentFeed의 작동 방식에 대해 간략하게 살펴보겠습니다. DocumentDB에서는 `ReadDocumentFeed` API를 통해 컬렉션 내에서 문서의 필드를 읽도록 지원합니다. 예를 들어, 다음 요청은 `serverlogs` 컬렉션의 내부에서 문서의 페이지를 반환합니다. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/smallcoll HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

`x-ms-max-item-count` 헤더를 사용하여 결과를 제한할 수 있고 이전 응답에서 반환된 `x-ms-continuation` 헤더로 요청을 다시 제출하여 읽기를 다시 시작할 수 있습니다. 단일 클라이언트에서 수행되는 경우 `ReadDocumentFeed`은 직렬로 파티션에 대한 결과를 통해 반복합니다. 

**직렬 읽기 문서 피드**

![DocumentDB ReadDocumentFeed 직렬 실행](./media/documentdb-change-feed/readfeedserial.png)

지원되는 [DocumentDB SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서의 피드를 검색할 수도 있습니다. 예를 들어, 다음 코드 조각에서는 .NET에서 ReadDocumentFeed를 수행하는 방법을 보여 줍니다.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

> [!NOTE]
> 변경 피드에는 SDK 버전 1.11.0 이상이 필요합니다(현재 비공개 미리 보기 버전에서 사용 가능).

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed의 분산 실행
테라바이트 이상의 데이터를 포함하거나 대량의 업데이트를 수집하는 컬렉션의 경우 단일 클라이언트 컴퓨터에서 읽기 피드의 직렬 실행은 실용적이지 않습니다. 이러한 빅 데이터 시나리오를 지원하기 위해 DocumentDB에서는 API를 제공하여 여러 클라이언트 판독기/소비자에게 `ReadDocumentFeed` 호출을 투명하게 배포합니다. 

**분산 읽기 문서 피드**

![DocumentDB ReadDocumentFeed 분산 실행](./media/documentdb-change-feed/readfeedparallel.png)

증분 변경 내용의 확장 가능한 처리를 제공하기 위해 DocumentDB에서는 파티션 키 범위에 따라 변경 피드 API의 확장 모델을 지원합니다.

* `ReadPartitionKeyRanges` 호출을 수행하는 컬렉션에 대한 파티션 키 범위의 목록을 가져올 수 있습니다. 
* 각 파티션 키 범위의 경우 해당 범위 내에서 파티션 키를 사용하여 문서를 읽는 `ReadDocumentFeed`을 수행할 수 있습니다.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>컬렉션에 대한 파티션 키 범위 검색
컬렉션 내에서 `pkranges` 리소스를 요청하여 파티션 키 범위를 검색할 수 있습니다. 예를 들어, 다음 요청은 `serverlogs` 컬렉션에 대한 파티션 키 범위 목록을 검색합니다.

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

이 요청에는 파티션 키 범위에 대한 메타데이터가 포함된 다음과 같은 응답을 반환합니다.

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**파티션 키 범위 속성**: 각 파티션 키 범위에는 다음 테이블에 있는 메타데이터 속성이 포함됩니다.

<table>
    <tr>
        <th>헤더 이름</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>파티션 키 범위에 대한 ID입니다. 각 컬렉션 내에서 안정적이고 고유한 ID입니다.</p>
            <p>파티션 키 범위에서 변경 내용을 읽는 다음 호출에서 사용해야 합니다.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>파티션 키 범위에 대한 파티션 키 해시의 최대 값입니다. 내부에 사용합니다.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>파티션 키 범위에 대한 파티션 키 해시의 최소 값입니다. 내부에 사용합니다.</td>
    </tr>       
</table>

지원되는 [DocumentDB Sdk](documentdb-sdk-dotnet.md) 중 하나를 사용하여 수행할 수 있습니다. 예를 들어, 다음 코드 조각에서는 .NET에서 파티션 키 범위를 검색하는 방법을 보여 줍니다.

    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
    FeedResponse<PartitionKeyRange> response;

    do
    {
        response = await client.ReadPartitionKeyRangeFeedAsync(collection);
        partitionKeyRanges.AddRange(response);
    }
    while (response.ResponseContinuation != null);

DocumentDB에서는 옵션 `x-ms-documentdb-partitionkeyrangeid` 헤더를 설정하여 파티션 키 범위당 문서 검색을 지원합니다. 

### <a name="performing-an-incremental-readdocumentfeed"></a>증분 ReadDocumentFeed 수행
ReadDocumentFeed는 DocumentDB 컬렉션의 변경 내용을 증분 처리하는 다음과 같은 시나리오/작업을 지원합니다.

* 처음부터 즉, 컬렉션 생성에서부터 문서에 대한 모든 변경 내용을 읽습니다.
* 현재 시간부터 문서의 향후 업데이트에 대한 모든 변경 내용을 읽습니다.
* 컬렉션의 논리 버전부터 문서에 대한 모든 변경 내용을 읽습니다(ETag). 증분 읽기 피드 요청에서 반환된 ETag에 따라 소비자의 검사점을 지정할 수 있습니다.

변경 내용에는 문서에 대한 삽입 및 업데이트가 포함됩니다. 삭제를 캡처하려면 문서 내에서 "soft delete" 속성을 사용하거나 [기본 제공 TTL 속성](documentdb-time-to-live.md)을 사용하여 변경 피드에서 보류 중인 삭제를 알려야 합니다.

다음 테이블에서는 ReadDocumentFeed 작업에 대한 요청 및 응답 헤더를 나열합니다.

**증분 ReadDocumentFeed에 대한 요청 헤더**:

<table>
    <tr>
        <th>헤더 이름</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>"증분 피드"로 설정하거나 그렇지 않으면 생략해야 합니다.</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>헤더 없음: 처음(컬렉션 생성)부터 모든 변경 내용을 반환합니다.</p>
            <p>"*": 컬렉션 내의 데이터에 대한 새 변경 내용을 모두 반환합니다.</p>
            <p>&lt;etag&gt;: ETag 컬렉션에 설정한 경우 해당 논리 타임스탬프 이후에 변경한 모든 내용을 반환합니다.</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>데이터를 읽는 파티션 키 범위 ID입니다.</td>
    </tr>
</table>

**증분 ReadDocumentFeed에 대한 응답 헤더**:

<table>
    <tr>
        <th>헤더 이름</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>응답에서 반환되는 최근 문서의 LSN(논리 시퀀스 번호)입니다.</p>
            <p>증분 ReadDocumentFeed는 If-None-Match에서 이 값을 다시 제출하여 다시 시작될 수 있습니다.</p>
        </td>
    </tr>
</table>

다음 논리적 버전/ETag `28535` 및 파티션 키 범위 = `16`의 컬렉션에서 모든 증분 변경 내용을 반환하는 샘플 요청입니다.

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

변경 내용은 파티션 키 범위 내의 각 파티션 키 값 안에서 시간 기준으로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다. 단일 페이지에 맞출 수 있는 것보다 결과가 많은 경우 이전 응답의 `etag`와 같은 값을 포함한 `If-None-Match` 헤더로 요청을 다시 제출하여 결과의 다음 페이지를 읽을 수 있습니다. 저장 프로시저 또는 트리거 내에서 여러 문서를 트랜잭션 방식으로 업데이트한 경우 동일한 응답 페이지 내에서 모두 반환됩니다.

.NET SDK는 컬렉션에 대한 변경 내용에 액세스하기 위해 `CreateDocumentChangeFeedQuery` 및 `ChangeFeedOptions` 도우미 클래스를 제공합니다. 다음 코드 조각에서는 단일 클라이언트에서 .NET SDK를 사용하여 처음부터 모든 변경 내용을 검색하는 방법을 보여 줍니다.

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
        FeedResponse<PartitionKeyRange> pkRangesResponse;

        do
        {
            pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(collection);
            partitionKeyRanges.AddRange(pkRangesResponse);
        }
        while (pkRangesResponse.ResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

다음 코드 조각에서는 변경 피드 지원과 앞의 함수를 사용하여 DocumentDB에서 실시간으로 변경 내용을 처리하는 방법을 보여 줍니다. 첫 번째 호출은 컬렉션에 있는 모든 문서를 반환하고 두 번째 호출은 최근 검사점 이후에 만들어진 만든 두 개의 문서만을 반환합니다.

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


선택적으로 이벤트를 처리하기 위해 클라이언트 쪽 논리를 사용하여 변경 피드를 필터링할 수도 있습니다. 예를 들어, 장치 센서에서 온도 변경 이벤트만을 처리하는 클라이언트 쪽 LINQ를 사용하는 코드 조각은 다음과 같습니다.

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

이 문서에서는 DocumentDB의 변경 피드 지원의 연습 및 DocumentDB REST API 및/또는 SDK를 사용하여 DocumentDB 데이터에 대한 변경 내용을 추적하는 방법을 제공합니다. 

## <a name="next-steps"></a>다음 단계
* [GitHub의 DocumentDB 변경 피드 코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed) 사용
* [DocumentDB의 리소스 모델 및 계층 구조](documentdb-resources.md)에 대해 자세히 알아보기
* [DocumentDB SDK](documentdb-sdk-dotnet.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 코딩 시작


<!--HONumber=Dec16_HO2-->


