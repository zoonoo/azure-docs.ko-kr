---
title: Azure Blob Storage에서 변경 피드 처리 (미리 보기) | Microsoft Docs
description: .NET 클라이언트 응용 프로그램에서 변경 피드 로그를 처리 하는 방법을 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 00805d6284c166d0ec03b9fe88482377a9488314
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084858"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob Storage에서 변경 피드 처리 (미리 보기)

변경 피드는 저장소 계정의 blob 및 blob 메타 데이터에 발생 하는 모든 변경 내용에 대 한 트랜잭션 로그를 제공 합니다. 이 문서에서는 blob 변경 피드 프로세서 라이브러리를 사용 하 여 변경 피드 레코드를 읽는 방법을 보여 줍니다.

변경 피드에 대 한 자세한 내용은 [Azure Blob Storage에서 피드 변경 (미리 보기)](storage-blob-change-feed.md)을 참조 하세요.

> [!NOTE]
> 변경 피드는 공개 미리 보기 상태 이며 **westcentralus** 및 **westus2** 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께이 기능에 대 한 자세한 내용은 [Azure Blob Storage에서 변경 피드 지원](storage-blob-change-feed.md)을 참조 하세요. 변경 피드 프로세서 라이브러리는 현재까지 변경 될 수 있으며,이 라이브러리가 일반 공급 될 때 변경 될 수 있습니다.

## <a name="get-the-blob-change-feed-processor-library"></a>Blob 변경 피드 프로세서 라이브러리 가져오기

1. Visual Studio에서 NuGet 패키지 소스에 `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` URL을 추가 합니다. 

   방법을 알아보려면 [패키지 원본](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)을 참조 하세요.

2. NuGet 패키지 관리자에서 **Microsoft Azure. Changefeed** 패키지를 찾아 프로젝트에 설치 합니다. 

   방법에 대 한 자세한 내용은 [패키지 찾기 및 설치](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)를 참조 하세요.

## <a name="connect-to-the-storage-account"></a>스토리지 계정에 연결

[Cloudstorageaccount. TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) 메서드를 호출 하 여 연결 문자열을 구문 분석 합니다. 

그런 다음 [CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet) 메서드를 호출 하 여 저장소 계정에서 Blob Storage 나타내는 개체를 만듭니다.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>변경 피드 초기화

다음 using 문을 코드 파일의 맨 위에 추가 합니다. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

그런 다음 **GetContainerReference** 메서드를 호출 하 여 **changefeed** 클래스의 인스턴스를 만듭니다. 변경 피드 컨테이너의 이름을 전달 합니다.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>레코드 읽기

> [!NOTE]
> 변경 피드는 저장소 계정에 있는 변경할 수 없는 읽기 전용 엔터티입니다. 원하는 수의 응용 프로그램은 각자의 편의를 위해 동시에 독립적으로 변경 피드를 읽고 처리할 수 있습니다. 응용 프로그램에서 레코드를 읽을 때 레코드는 변경 피드에서 제거 되지 않습니다. 사용 되는 각 판독기의 읽기 또는 반복 상태는 응용 프로그램 에서만 독립적이 고 유지 관리 됩니다.

레코드를 읽는 가장 간단한 방법은 **ChangeFeedReader** 클래스의 인스턴스를 만드는 것입니다. 

이 예에서는 변경 피드의 모든 레코드를 반복한 다음 각 레코드에서 몇 가지 값을 콘솔에 출력 합니다. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>저장 된 위치에서 레코드 읽기 재개

변경 피드에 읽기 위치를 저장 하 고 나중에 레코드 반복을 다시 시작할 수 있습니다. 언제 든 지 **ChangeFeedReader (SerializeState)** 메서드를 사용 하 여 변경 피드의 반복 상태를 저장할 수 있습니다. 상태는 **문자열** 이며 응용 프로그램은 응용 프로그램의 디자인 (예: 데이터베이스 또는 파일)에 따라 해당 상태를 저장할 수 있습니다.

```csharp
    string currentReadState = processor.SerializeState();
```

**CreateChangeFeedReaderFromPointerAsync** 메서드를 사용 하 여 **ChangeFeedReader** 를 만들어 마지막 상태에서 계속 해 서 레코드를 반복할 수 있습니다.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>레코드의 스트림 처리

도착할 때 변경 피드 레코드를 처리 하도록 선택할 수 있습니다. [사양](storage-blob-change-feed.md#specifications)을 참조 하세요.

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>시간 범위 내에서 레코드 읽기

변경 피드는 변경 이벤트 시간에 따라 매시간 세그먼트로 구성 됩니다. [사양](storage-blob-change-feed.md#specifications)을 참조 하세요. 특정 시간 범위에 속하는 변경 피드 세그먼트에서 레코드를 읽을 수 있습니다.

이 예제에서는 모든 세그먼트의 시작 시간을 가져옵니다. 그런 다음 시작 시간이 마지막으로 사용할 수 있는 세그먼트의 시간을 초과 하거나 원하는 범위의 종료 시간을 초과할 때까지 해당 목록을 반복 합니다. 

### <a name="selecting-segments-for-a-time-range"></a>시간 범위에 대 한 세그먼트 선택

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>세그먼트의 레코드 읽기

개별 세그먼트 또는 세그먼트 범위에서 레코드를 읽을 수 있습니다.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>시간에서 시작 하는 레코드 읽기

시작 세그먼트에서 끝까지 변경 피드의 레코드를 읽을 수 있습니다. 시간 범위 내에서 레코드를 읽는 것과 마찬가지로 세그먼트를 나열 하 고 반복을 시작할 세그먼트를 선택할 수 있습니다.

이 예에서는 처리할 첫 번째 세그먼트의 [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) 을 가져옵니다.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

이 예에서는 시작 세그먼트의 [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) 에서 시작 하는 변경 피드 레코드를 처리 합니다.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> 의 세그먼트는 하나 이상의 *Chunkfilepath*에 변경 피드 로그를 포함할 수 있습니다. 여러 *Chunkfilepath* 의 경우 시스템은 게시 처리량을 관리 하기 위해 레코드를 여러 분할로 내부적으로 분할 했습니다. 세그먼트의 각 파티션에는 상호 배타적인 blob에 대 한 변경 내용이 포함 되며 순서를 위반 하지 않고 독립적으로 처리할 수 있습니다. 시나리오에 가장 효율적인 경우 **ChangeFeedSegmentShardReader** 클래스를 사용 하 여 분할 된 수준에서 레코드를 반복할 수 있습니다.

## <a name="next-steps"></a>다음 단계

변경 피드 로그에 대해 자세히 알아보세요. [Azure Blob Storage에서 피드 변경 (미리 보기)을](storage-blob-change-feed.md) 참조 하세요.
