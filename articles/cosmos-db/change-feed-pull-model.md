---
title: 변경 피드 풀 모델
description: Azure Cosmos DB 변경 피드 끌어오기 모델을 사용 하 여 변경 피드를 읽는 방법 및 끌어오기 모델과 변경 피드 프로세서 간의 차이점에 대해 알아봅니다.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116716"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB에서 피드 끌어오기 모델 변경

변경 피드 끌어오기 모델을 사용 하 여 원하는 속도로 Azure Cosmos DB 변경 피드를 사용할 수 있습니다. [변경 피드 프로세서](change-feed-processor.md)를 사용 하 여 수행할 수 있는 것 처럼 변경 피드 끌어오기 모델을 사용 하 여 여러 변경 피드 소비자의 변경 내용 처리를 병렬화 할 수 있습니다.

> [!NOTE]
> 변경 피드 끌어오기 모델은 현재 [Azure Cosmos DB .NET SDK 에서만 미리 보기로](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) 제공 됩니다. 다른 SDK 버전에서는 미리 보기를 아직 사용할 수 없습니다.

## <a name="consuming-an-entire-containers-changes"></a>전체 컨테이너의 변경 내용 사용

`FeedIterator`끌어오기 모델을 사용 하 여 변경 피드를 처리 하는를 만들 수 있습니다. 를 처음 만들 때 `FeedIterator` 내에서 선택 항목을 지정할 수 있습니다 `StartTime` `ChangeFeedRequestOptions` . 지정 `StartTime` 하지 않으면 현재 시간이 됩니다.

는 `FeedIterator` 두 가지 형태로 제공 됩니다. 엔터티 개체를 반환 하는 아래 예제 외에도 지원 요청을 받을 수 있습니다 `Stream` . 스트림을 사용 하면 데이터를 먼저 deserialize 하지 않고 데이터를 읽고 클라이언트 리소스를 저장할 수 있습니다.

`FeedIterator`엔터티 개체를 반환 하는 (이 경우 개체)를 가져오는 예제는 다음과 같습니다 `User` .

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

을 반환 하는를 가져오는 예제는 `FeedIterator` 다음과 같습니다 `Stream` .

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

를 사용 `FeedIterator` 하면 고유한 속도로 전체 컨테이너의 변경 피드를 쉽게 처리할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>파티션 키의 변경 내용 사용

특정 파티션 키의 변경 내용만 처리 하려는 경우도 있습니다. `FeedIterator`특정 파티션 키에 대 한를 가져와서 전체 컨테이너에 대해 다음과 같은 방법으로 변경 내용을 처리할 수 있습니다.

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>병렬 처리에 FeedRange 사용

[변경 피드 프로세서](change-feed-processor.md)에서 작업은 여러 소비자에 게 자동으로 분산 됩니다. 변경 피드 끌어오기 모델에서를 사용 `FeedRange` 하 여 변경 피드의 처리를 병렬화 할 수 있습니다. 는 `FeedRange` 파티션 키 값의 범위를 나타냅니다.

컨테이너의 범위 목록을 가져오는 방법을 보여 주는 예제는 다음과 같습니다.

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

컨테이너에 대 한 FeedRanges 목록을 가져오면 `FeedRange` [실제 파티션당](partition-data.md#physical-partitions)하나를 받게 됩니다.

를 사용 하 여 `FeedRange` `FeedIterator` 여러 컴퓨터 또는 스레드에서 변경 피드의 처리를 병렬화 하는을 만들 수 있습니다. 전체 컨테이너에 대해 단일를 가져오는 방법을 보여 준 이전 예제와 `FeedIterator` 는 달리를 사용 하 여 `FeedRange` 변경 피드를 병렬로 처리할 수 있는 여러 FeedIterators 얻을 수 있습니다.

FeedRanges를 사용 하려는 경우 FeedRanges를 가져오고 해당 컴퓨터에 배포 하는 orchestrator 프로세스가 필요 합니다. 이 배포는 다음과 같을 수 있습니다.

* `FeedRange.ToJsonString`이 문자열 값을 사용 하 고 배포 합니다. 소비자는와 함께이 값을 사용할 수 있습니다.`FeedRange.FromJsonString`
* 배포가 in-process 인 경우 `FeedRange` 개체 참조를 전달 합니다.

다음은 병렬로 읽는 두 개의 가상 컴퓨터를 사용 하 여 컨테이너의 변경 피드의 시작 부분에서 읽는 방법을 보여 주는 샘플입니다.

컴퓨터 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

컴퓨터 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>연속 토큰 저장

연속 토큰을 만들어의 위치를 저장할 수 있습니다 `FeedIterator` . 연속 토큰은 FeedIterator의 마지막으로 처리 된 변경 내용 추적을 유지 하는 문자열 값입니다. 이렇게 하면 나중에를 `FeedIterator` 다시 시작할 수 있습니다. 다음 코드는 컨테이너를 만든 이후의 변경 피드를 통해 읽습니다. 사용할 수 있는 변경 내용이 더 이상 없는 경우에는 변경 피드 사용을 나중에 다시 시작할 수 있도록 연속 토큰을 유지 합니다.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>변경 피드 프로세서와 비교

대부분의 시나리오에서는 [변경 피드 프로세서나](change-feed-processor.md) 끌어오기 모델을 사용 하 여 변경 피드를 처리할 수 있습니다. 끌어오기 모델의 연속 토큰 및 변경 피드 프로세서의 임대 컨테이너는 변경 피드의 마지막 처리 된 항목 (또는 항목 일괄 처리)에 대 한 "책갈피"입니다.
그러나 연속 토큰을 임대 컨테이너로 변환할 수 없으며 그 반대의 경우도 마찬가지입니다.

이러한 시나리오에서는 끌어오기 모델을 사용 하는 것을 고려해 야 합니다.

- 변경 피드의 기존 데이터를 한 번만 읽도록 하려고 합니다.
- 특정 파티션 키의 변경 내용만 읽으려고 합니다.
- 푸시 모델을 원하지 않고 사용자 고유의 속도로 변경 피드를 사용 하려고 합니다.

변경 피드 프로세서와 끌어오기 모델의 몇 가지 주요 차이점은 다음과 같습니다.

|  | 변경 피드 프로세서| 풀 모델 |
| --- | --- | --- |
| 변경 피드 처리에서 현재 지점을 추적 하는 중 | 임대 (Azure Cosmos DB 컨테이너에 저장) | 연속 토큰 (메모리에 저장 되거나 수동으로 지속형) |
| 이전 변경 내용을 재생 하는 기능 | 예, 푸시 모델 사용 | 예, 끌어오기 모델 사용|
| 이후 변경 내용 폴링 | 사용자 지정에 따라 변경 내용을 자동으로 확인 합니다.`WithPollInterval` | Manual |
| 전체 컨테이너의 변경 내용 처리 | 예, 그리고 동일한 컨테이너에서 사용 하는 여러 스레드/컴퓨터 간에 자동으로 병렬화 됩니다.| 예, FeedTokens를 사용 하 여 수동으로 병렬화 |
| 단일 파티션 키에서 변경 내용 처리 | 지원되지 않음 | 예|
| 지원 수준 | 일반 공급 | 미리 보기 |

## <a name="next-steps"></a>다음 단계

* [변경 피드 개요](change-feed.md)
* [변경 피드 프로세서 사용](change-feed-processor.md)
* [Azure Functions 트리거](change-feed-functions.md)