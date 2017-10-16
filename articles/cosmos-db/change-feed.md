---
title: "Azure Cosmos DB에서 변경 피드 지원 사용 | Microsoft Docs"
description: "Azure Cosmos DB의 변경 피드 지원을 사용하여 문서에서 변경 내용을 추적하고 트리거와 마찬가지로 이벤트 기반 처리를 수행하고 캐시 및 분석 시스템을 최신 상태로 유지합니다."
keywords: "변경 피드"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.openlocfilehash: 16bd85065f77612ac342ae4a8b500e0c7fa2a078
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB에서 변경 피드 지원 사용
[Azure Cosmos DB](../cosmos-db/introduction.md)는 빠르고 유연성 있는, 전역적으로 복제된 데이터베이스 서비스로 읽기 및 쓰기에 대해 한 자릿수 밀리초인 예측 가능한 대기 시간 동안 대량의 트랜잭션 및 운영 데이터를 저장하는 데 사용됩니다. 따라서 IoT, 게임, 소매 및 운영 로깅 응용 프로그램에 적합합니다. 이러한 응용 프로그램에서 일반적인 디자인 패턴은 Azure Cosmos DB 데이터에 대한 변경 내용을 추적하고 구체화된 뷰를 업데이트하며 실시간 분석을 수행하고 콜드 저장소에 데이터를 보관하며 이러한 변경 내용을 기반으로 특정 이벤트에 대한 알림을 트리거하는 것입니다. Azure Cosmos DB의 **변경 피드 지원**을 사용하면 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

Azure Cosmos DB에서는 변경 피드 지원을 사용하여 Azure Cosmos DB 컬렉션 내 문서의 정렬된 목록을 수정된 순서로 제공합니다. 컬렉션 내에서 데이터에 대한 수정을 수신 대기하고 다음과 같은 작업을 수행하기 위해 이 피드를 사용할 수 있습니다.

* 문서를 삽입하거나 수정하는 경우 API에 대한 호출 트리거
* 업데이트에 대한 실시간(스트림) 처리 수행
* 캐시, 검색 엔진 또는 데이터 웨어하우스와 데이터 동기화

Azure Cosmos DB의 변경 내용이 유지되면 비동기적으로 처리되고 병렬 처리를 위해 한 명 이상의 소비자에게 분산될 수 있습니다. 변경 피드에 대한 API 및 이를 사용하여 확장 가능한 실시간 응용 프로그램을 빌드하는 방법에 대해 살펴보겠습니다. 이 문서에서는 Azure Cosmos DB 변경 피드 및 DocumentDB API를 사용하는 방법을 보여줍니다. 

![Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 이 시점에 변경 피드 지원은 DocumentDB API에만 제공됩니다. Graph API 및 Table API는 현재 지원되지 않습니다.

## <a name="use-cases-and-scenarios"></a>사용 사례 및 시나리오
변경 피드를 사용하면 많은 양의 쓰기가 포함된 큰 데이터 집합을 효율적으로 처리할 수 있고 변경된 내용을 식별하는 전체 데이터 집합을 쿼리하는 대안을 제공합니다. 예를 들어, 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.
* 응용 프로그램 수준 데이터 계층 및 보관을 구현합니다. 즉, Azure Cosmos DB에 "핫 데이터"를 저장하고 "콜드 데이터"를 [Azure Blob Storage](../storage/common/storage-introduction.md) 또는 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)로 내보냅니다.
* [Apache Hadoop](run-hadoop-with-hdinsight.md)를 사용하여 데이터에 대한 Batch 분석을 구현합니다.
* Azure Cosmos DB를 사용하여 [Azure의 람다 파이프라인](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)을 구현합니다. Azure Cosmos DB는 수집 및 쿼리를 모두 처리할 수 있는 확장성이 뛰어난 데이터베이스 솔루션을 제공하고 TCO가 낮은 람다 아키텍처를 구현합니다. 
* 다른 파티션 구성표를 사용하여 다른 Azure Cosmos DB 계정에 대한 중단 시간이 없는 마이그레이션을 수행합니다.

**수집 및 쿼리에 Azure Cosmos DB를 사용하는 람다 파이프라인:**

![수집 및 쿼리에 대한 Azure Cosmos DB 기반 람다 파이프라인](./media/change-feed/lambda.png)

Azure Cosmos DB를 사용하여 장치, 센서, 인프라 및 응용 프로그램에서 이벤트 데이터를 수신하고 저장하며 [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) 또는 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다. 

[서버를 사용하지 않는](http://azure.com/serverless) 웹 및 모바일 앱 내에서는 [Azure Functions](../azure-functions/functions-bindings-documentdb.md) 또는 [App Services](https://azure.microsoft.com/services/app-service/)를 사용하여 해당 장치에 푸시 알림을 보내는 등의 특정 작업을 트리거하기 위해 고객의 프로필, 기본 설정 또는 위치에 대한 변경 내용과 같은 이벤트를 추적할 수 있습니다. 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Azure Cosmos DB에서 변경 피드의 작동 방식
Azure Cosmos DB에서는 증분 방식으로 Azure Cosmos DB 컬렉션에 이뤄진 업데이트를 읽는 기능을 제공합니다. 이 변경 피드에는 다음과 같은 속성이 있습니다.

* 변경 내용이 Azure Cosmos DB에서 지속되면 비동기적으로 처리될 수 있습니다.
* 컬렉션 내 문서에 대한 변경 내용은 변경 피드에서 즉시 사용할 수 있습니다.
* 각 문서에 대한 변경 내용이 한 번 변경 피드에 즉시 표시되고 클라이언트는 해당 검사점 논리를 관리합니다. 변경 피드 프로세서 라이브러리는 자동 검사점 및 “최소 한 번” 의미 체계를 제공합니다.
* 지정된 문서에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.
* 변경 피드는 각 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.
* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.
* 변경 내용은 파티션 키 범위에서 사용할 수 있습니다. 이 기능을 사용하면 대규모 컬렉션의 변경 내용을 여러 소비자/서버에 의해 병렬로 처리할 수 있습니다.
* 응용 프로그램은 동일한 컬렉션에서 동시에 여러 변경 피드를 요청할 수 있습니다.

Azure Cosmos DB의 변경 피드는 모든 계정에 기본적으로 사용됩니다. 쓰기 지역 또는 [읽기 지역](distribute-data-globally.md)에서 [프로비전된 처리량](request-units.md)을 사용하여 Azure Cosmos DB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다. 변경 피드는 컬렉션 내의 문서에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 문서 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](time-to-live.md)을 통해 문서에 대한 제한 만료 기간을 설정할 수 있습니다. 예를 들어, 24시간으로 설정하고 해당 속성의 값을 사용하여 삭제를 캡처합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다. 변경 피드는 문서 컬렉션 내에서 각 파티션 키 범위에 대해 사용할 수 있으며 따라서 병렬 처리를 위해 한 명 이상의 소비자에게 배포할 수 있습니다. 

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/changefeedvisual.png)

클라이언트 코드에서 변경 피드를 구현하는 방법에는 몇 가지 옵션이 있습니다. 다음에 이어지는 섹션에서는 Azure Cosmos DB REST API 및 DocumentDB SDK를 사용하여 변경 피드를 구현하는 방법을 설명합니다. 그러나 .NET 응용 프로그램의 경우, 새로운 [변경 피드 프로세서 라이브러리](#change-feed-processor)를 사용하여 변경 피드의 이벤트를 처리하는 것이 좋습니다. 이 라이브러리 사용 시, 전체 파티션의 변경 사항 읽기가 간소해지고 여러 스레드를 병렬로 실행할 수 있습니다. 

## <a id="rest-apis"></a>REST API 및 DocumentDB SDK 사용
Azure Cosmos DB에서는 **컬렉션**이라는 저장소 및 처리량의 탄력적인 컨테이너를 제공합니다. 확장성 및 성능을 위해 [파티션 키](partition-data.md)를 사용하여 컬렉션 내의 데이터를 논리적으로 그룹화합니다. Azure Cosmos DB에서는 ID(읽기/가져오기), 쿼리 및 읽기-피드(검색) 기준 조회를 포함하여 이 데이터에 액세스하기 위한 다양한 API를 제공합니다. 변경 피드는 DocumentDB의 `ReadDocumentFeed` API에 두 가지 새로운 요청 헤더를 채워서 가져올 수 있으며 파티션 키 범위에서 동시에 처리할 수 있습니다.

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
ReadDocumentFeed의 작동 방식에 대해 간략하게 살펴보겠습니다. Azure Cosmos DB에서는 `ReadDocumentFeed` API를 통해 컬렉션 내에서 문서의 피드를 읽도록 지원합니다. 예를 들어, 다음 요청은 `serverlogs` 컬렉션의 내부에서 문서의 페이지를 반환합니다. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

`x-ms-max-item-count` 헤더를 사용하여 결과를 제한할 수 있고 이전 응답에서 반환된 `x-ms-continuation` 헤더로 요청을 다시 제출하여 읽기를 다시 시작할 수 있습니다. 단일 클라이언트에서 수행되는 경우, `ReadDocumentFeed`는 전체 파티션의 결과를 순차적으로 반복합니다. 

**문서 피드의 순차적 읽기**

지원되는 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서의 피드를 검색할 수도 있습니다. 예를 들어, 다음 코드 조각은 .NET에서 [ReadDocumentFeedAsync 메서드](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet)를 사용하는 방법을 보여줍니다.

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed의 분산 실행
테라바이트 이상의 데이터를 포함하거나 대량의 업데이트를 수집하는 컬렉션의 경우, 단일 클라이언트 컴퓨터에서 읽기 피드를 순차적으로 실행하면 실용적이지 않습니다. 이러한 빅 데이터 시나리오를 지원하기 위해 Azure Cosmos DB에서는 API를 제공하여 여러 클라이언트 판독기/소비자에게 `ReadDocumentFeed` 호출을 투명하게 배포합니다. 

**분산 읽기 문서 피드**

증분 변경 내용의 확장 가능한 처리를 제공하기 위해 Azure Cosmos DB에서는 파티션 키 범위에 따라 변경 피드 API의 확장 모델을 지원합니다.

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

지원되는 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 수행할 수 있습니다. 예를 들어, 다음 코드 조각은 [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet) 메서드를 사용하여 .NET에서 파티션 키 범위를 검색하는 방법을 보여 줍니다.

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

Azure Cosmos DB에서는 옵션 `x-ms-documentdb-partitionkeyrangeid` 헤더를 설정하여 파티션 키 범위당 문서 검색을 지원합니다. 

### <a name="performing-an-incremental-readdocumentfeed"></a>증분 ReadDocumentFeed 수행
ReadDocumentFeed는 Azure Cosmos DB 컬렉션의 변경 내용을 증분 처리하는 다음과 같은 시나리오/작업을 지원합니다.

* 처음부터 즉, 컬렉션 생성에서부터 문서에 대한 모든 변경 내용을 읽습니다.
* 현재 시간부터 문서의 향후 업데이트에 대한 모든 변경 내용 또는 사용자가 지정한 시간 이후의 변경 내용을 읽습니다.
* 컬렉션의 논리 버전부터 문서에 대한 모든 변경 내용을 읽습니다(ETag). 증분 읽기 피드 요청에서 반환된 ETag에 따라 소비자의 검사점을 지정할 수 있습니다.

변경 내용에는 문서에 대한 삽입 및 업데이트가 포함됩니다. 삭제를 캡처하려면 문서 내에서 "soft delete" 속성을 사용하거나 [기본 제공 TTL 속성](time-to-live.md)을 사용하여 변경 피드에서 보류 중인 삭제를 알려야 합니다.

다음 테이블에서는 ReadDocumentFeed 작업에 대한 [요청](/rest/api/documentdb/common-documentdb-rest-request-headers.md) 및 [응답 헤더](/rest/api/documentdb/common-documentdb-rest-response-headers.md)를 나열합니다.

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
        <td>If-Modified-Since</td> 
        <td>RFC 1123 시간 형식: If-None-Match가 지정된 경우 무시합니다.</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>데이터를 읽는 파티션 키 범위 ID입니다.</td>
    </tr>
</table>

**증분 ReadDocumentFeed에 대한 응답 헤더**:

<table> <tr>
        <th>헤더 이름</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>응답에서 반환되는 최근 문서의 LSN(논리 시퀀스 번호)입니다.</p>
            <p>증분 ReadDocumentFeed는 If-None-Match에서 이 값을 다시 제출하여 다시 시작할 수 있습니다.</p>
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

변경 내용은 파티션 키 범위 내의 각 파티션 키 값 안에서 시간 기준으로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다. 단일 페이지에 맞출 수 있는 것보다 결과가 많은 경우 이전 응답의 `etag`와 같은 값을 포함한 `If-None-Match` 헤더로 요청을 다시 제출하여 결과의 다음 페이지를 읽을 수 있습니다. 저장 프로시저 또는 트리거 내에서 여러 문서를 트랜잭션 방식으로 삽입 또는 업데이트한 경우 동일한 응답 페이지 내에서 모두 반환됩니다.

> [!NOTE]
> 변경 피드를 사용하면 저장된 프로시저 또는 트리거 내에서 여러 문서를 삽입 또는 업데이트할 때 `x-ms-max-item-count`에 지정된 것보다 더 많은 항목이 한 페이지에 반환됩니다. 

.NET SDK(1.17.0)를 사용하는 경우 `CreateDocumentChangeFeedQuery`를 호출할 때 `StartTime` 이후에 변경된 문서를 즉시 반환하도록 `ChangeFeedOptions`의 `StartTime` 필드를 설정합니다. REST API를 사용하여 `If-Modified-Since`를 지정하면 사용자 요청은 문서 자체가 아닌 응답 헤더의 연속 토큰 또는 `etag`를 반환하게 됩니다. 지정된 시간이 수정된 문서를 반환하려면 `If-None-Match`를 사용하는 다음 요청에 연속 토큰 `etag`를 사용하여 실제 문서를 반환해야 합니다. 

.NET SDK는 컬렉션의 변경 내용에 액세스할 수 있도록 [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) 및 [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) 도우미 클래스를 제공합니다. 다음 코드 조각에서는 단일 클라이언트에서 .NET SDK를 사용하여 처음부터 모든 변경 내용을 검색하는 방법을 보여 줍니다.

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

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
            FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>();

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
다음 코드 조각에서는 변경 피드 지원과 앞의 함수를 사용하여 Azure Cosmos DB에서 실시간으로 변경 내용을 처리하는 방법을 보여 줍니다. 첫 번째 호출은 컬렉션에 있는 모든 문서를 반환하고 두 번째 호출은 최근 검사점 이후에 만들어진 만든 두 개의 문서만을 반환합니다.

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

선택적으로 이벤트를 처리하기 위해 클라이언트 쪽 논리를 사용하여 변경 피드를 필터링할 수도 있습니다. 예를 들어, 장치 센서에서 온도 변경 이벤트만을 처리하는 클라이언트 쪽 LINQ를 사용하는 코드 조각은 다음과 같습니다.

```csharp
FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>변경 피드 프로세서 라이브러리
또 다른 옵션은 [Azure Cosmos DB 변경 피드 프로세서 라이브러리](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed)를 사용하는 것입니다. 이 라이브러리 사용 시, 여러 소비자에게 변경 피드의 이벤트 처리를 쉽게 분산할 수 있습니다. 이 라이브러리는 .NET 플랫폼에서 변경 피드 독자(reader)를 구축하는 데 유용합니다. 다른 Cosmos DB SDK에 포함된 메서드에 변경 피드 프로세서 라이브러리를 사용하면, 다음 워크플로가 간소화됩니다. 

* 데이터가 여러 파티션에 저장되어 있는 경우, 변경 피드에서 업데이트 풀링
* 컬렉션 간, 데이터 이동 또는 복제
* 데이터 및 변경 피드의 업데이트에 의해 트리거되는 작업의 병렬 실행 

Cosmos SDK에서 API를 사용하면 각 파티션의 변경 피드 업데이트에 정확하게 액세스할 수 있는 반면, 변경 피드 프로세서 라이브러리를 사용하면 전체 파티션의 변경 사항 읽기가 간소해지고 여러 스레드를 병렬로 실행할 수 있습니다. 변경 피드 프로세서는 수동으로 각 컨테이너에서 변경 내용을 읽고 각 파티션의 연속 토큰을 저장하지 않고 임대 메커니즘을 사용하여 전체 파티션의 변경 사항 읽기를 자동으로 관리합니다.

이 라이브러리는 NuGet 패키지인  [Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)로 제공되며, 소스 코드에서 Github [샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)로 사용할 수 있습니다. 

### <a name="understanding-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리의 이해 

변경 피드 프로세서를 구현하는 4개의 주요 구성 요소는 '모니터링되는 컬렉션, 임대 컬렉션, 프로세서 호스트, 소비자'입니다. 

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

### <a name="using-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 사용 
다음 섹션에서는 소스 컬렉션에서 대상 컬렉션에 변경 내용을 복제하는 컨텍스트에서 변경 피드 프로세서 라이브러리를 사용하는 방법을 설명합니다. 여기에서 소스 컬렉션은 변경 피드 프로세서에서 모니터링되는 컬렉션입니다. 

**변경 피드 프로세서 NuGet 패키지를 설치하고 포함하기** 

변경 피드 프로세서 NuGet 패키지를 설치하기 전에 먼저 다음을 설치합니다. 
* Microsoft.Azure.DocumentDB, 버전 1.13.1 이상 
* Newtonsoft.Json, 9.0.1 버전 이상 `Microsoft.Azure.DocumentDB.ChangeFeedProcessor` 설치 및 참조로 포함합니다.

**모니터링되는 컬렉션, 임대 및 대상 컬렉션 만들기** 

변경 피드 프로세서 라이브러리를 사용하려면 프로세서 호스트를 실행하기 전에 임대 컬렉션을 생성해야 합니다. 앞서 언급했지만, 임대 컬렉션은 변경 피드 프로세서를 실행하는 곳과 가장 근접한 쓰기 영역에서 다른 계정으로 저장하는 것이 좋습니다. 이 데이터 이동 예제에서는 변경 피드 프로세서 호스트를 실행하기 전에 대상 컬렉션을 만들어야 합니다. 샘플 코드에서 도우미 메서드를 호출하여 모니터링되는 컬렉션, 임대되는 컬렉션, 대상 컬렉션이 존재하지 않을 경우 이러한 컬렉션을 만들 수 있습니다. 

> [!WARNING]
> 응용 프로그램이 Azure Cosmos DB와 통신하기 위해 처리량을 예약할 때 컬렉션을 만드는 것은 가격에 영향을 미칩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 

*프로세서 호스트 만들기*

`ChangeFeedProcessorHost` 클래스는 검사점 및 파티션 임대 관리를 제공하는 이벤트 처리기 구현을 위한 스레드 안전, 다중 프로세스, 안전한 런타임 환경을 제공합니다. `ChangeFeedProcessorHost` 클래스를 사용하려면 `IChangeFeedObserver`를 구현하면 됩니다. 이 인터페이스는 세 가지 메서드가 포함합니다.

* `OpenAsync`: 이 함수는 변경 피드 관찰자를 열 때 호출됩니다. 소비자/관찰자가 열릴 때 특정 작업을 수행하도록 수정할 수 있습니다.  
* `CloseAsync`: 이 함수는 변경 피드 관찰자를 종료할 때 호출됩니다. 소비자/관찰자가 닫힐 때 특정 작업을 수행하도록 함수를 수정할 수 있습니다.  
* `ProcessChangesAsync`: 이 함수는 변경 피드에서 문서의 새로운 변경 내용을 사용할 수 있을 때 호출됩니다. 모든 변경 피드 업데이트 시 특정 작업을 수행하도록 함수를 수정할 수 있습니다.  

이 예제에서는 `DocumentFeedObserver` 클래스를 통해 `IChangeFeedObserver` 인터페이스를 구현합니다. 여기서 `ProcessChangesAsync` 함수는 변경 피드에서 대상 컬렉션에 문서를 upsert(업데이트)합니다. 이 예제는 데이터 집합의 파티션 키를 변경하기 위해 하나의 컬렉션에서 다른 컬렉션으로 데이터를 이동하는 데 유용합니다. 

*프로세서 호스트 실행*

이벤트 처리를 시작하기 전에, 변경 피드 옵션 및 변경 피드 호스트 옵션을 모두 사용자 지정할 수 있습니다. 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
사용자 지정할 수 있는 특정 필드는 다음 표에 요약되어 있습니다. 

**변경 피드 옵션**:
<table>
    <tr>
        <th>속성 이름</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>Azure Cosmos DB 데이터베이스 서비스의 열거 작업에서 반환할 항목의 최대 수를 가져오거나 설정합니다.</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>Azure Cosmos DB 데이터베이스 서비스에서 현재 요청에 대한 파티션 키 범위 ID를 가져오거나 설정합니다.</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>Azure Cosmos DB 데이터베이스 서비스에서 요청 연속 토큰을 가져오거나 설정합니다.</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>Azure Cosmos DB 데이터베이스 서비스에서 세션 일관성에 사용할 세션 토큰을 가져오거나 설정합니다.</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>Azure Cosmos DB 데이터베이스 서비스에서 변경 피드가 처음(true)부터 시작해야 하는지 아니면 현재(false)부터 시작해야 하는지를 가져오거나 설정합니다. 기본적으로 현재(false)부터 시작합니다.</td>
    </tr>
</table>

**변경 피드 호스트 옵션**:
<table>
    <tr>
        <th>속성 이름</th>
        <th>형식</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>TimeSpan</td>
        <td>ChangeFeedEventHost 인스턴스에서 현재 보유한 파티션의 모든 임대에 대한 간격입니다.</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>TimeSpan</td>
        <td>파티션이 알려진 호스트 인스턴스 간에 균등하게 배포되는지를 계산하는 태스크를 시작하는 간격입니다.</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>TimeSpan</td>
        <td>파티션을 나타내는 임대에 대한 임대 기간인 간격입니다. 이 간격 내에서 임대를 갱신하지 않으면 기간이 만료되어 다른 ChangeFeedEventHost 인스턴스로 파티션 소유권이 이동합니다.</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>TimeSpan</td>
        <td>현재 변경 내용이 모두 삭제된 후에 피드에 대한 새로운 변경 내용의 파티션을 폴링하는 작업 간의 지연입니다.</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>검사점 임대에 대한 빈도입니다.</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>호스트의 최소 파티션 수입니다.</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>호스트에 가능한 최대 파티션 수입니다.</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Bool</td>
        <td>호스트의 시작에서 모든 기존 임대를 삭제하고 호스트를 처음부터 다시 시작해야 하는지 여부입니다.</td>
    </tr>
</table>


이벤트 처리를 시작하려면 Azure Cosmos DB 컬렉션에 적절한 매개 변수를 제공하여 `ChangeFeedProcessorHost`를 인스턴스화합니다. 그런 다음 `RegisterObserverAsync`를 호출하여 `IChangeFeedObserver`(이 예제의 DocumentFeedObserver) 구현을 런타임에 등록합니다. 이 시점에서 호스트는 "greedy" 알고리즘을 사용하여 Azure Cosmos DB 컬렉션의 모든 파티션 키 범위에서 임대를 획득하려 합니다. 이러한 임대는 지정된 시간 프레임 동안 지속되며 갱신되어야 합니다. 새 노드(이 예제에서는 작업자(worker) 인스턴스)가 온라인 상태가 되면 임대 예약을 하고, 각 호스트가 더 많은 임대를 획득하려 하면서 시간이 지남에 따라 노드 간에 부하가 이동합니다. 

이 샘플 코드에서는 팩터리 클래스(DocumentFeedObserverFactory.cs)를 사용하여 관찰자를 만들고 `RegistObserverFactoryAsync`로 관찰자를 등록합니다. 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
시간이 지남에 따라 평형이 설정됩니다. 이 동적 기능을 사용하면 확장 및 축소 모두에 대해 CPU 기반의 자동 크기 조정을 소비자에게 적용할 수 있습니다. 변경 내용을 소비자가 처리할 수 있는 것보다 빠르게 Azure Cosmos DB에서 사용할 수 있는 경우 소비자에 대한 CPU가 증가하여 작업자 인스턴스 수의 크기를 자동으로 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [GitHub의 Azure Cosmos DB 변경 피드 코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed) 사용
* [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 또는 [REST API](/rest/api/documentdb/)를 사용하여 코딩 시작
