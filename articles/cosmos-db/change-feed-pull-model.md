---
title: 변경 피드 풀 모델
description: Azure Cosmos DB 변경 피드 끌어오기 모델을 사용하여 끌어오기 모델과 변경 피드 프로세서의 차이점 및 변경 피드를 읽는 방법 알아보기
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 6d2f39eae94b217ad1f95a6a559aa3e1044d10da
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93072695"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드 끌어오기 모델
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

변경 피드 끌어오기 모델을 사용하면 Azure Cosmos DB 변경 피드를 원하는 속도로 사용할 수 있습니다. [변경 피드 프로세서](change-feed-processor.md)로 이미 수행하듯이, 변경 피드 끌어오기 모델을 사용하여 다수의 변경 피드 소비자에 대한 변경 처리를 병렬화할 수 있습니다.

> [!NOTE]
> 변경 피드 끌어오기 모델은 현재 [Azure Cosmos DB .NET SDK에서만 미리 보기](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview)로 제공됩니다. 다른 SDK 버전에서는 아직 미리 보기를 사용할 수 없습니다.

## <a name="comparing-with-change-feed-processor"></a>변경 피드 프로세서와 비교

많은 시나리오에서 [변경 피드 프로세서](change-feed-processor.md) 또는 끌어오기 모델을 사용하여 변경 피드를 처리할 수 있습니다. 끌어오기 모델의 연속 토큰 및 변경 피드 프로세서의 임대 컨테이너는 변경 피드에서 마지막으로 처리된 항목(또는 항목의 모음)에 대한 "책갈피"입니다.

하지만 연속 토큰을 임대 컨테이너로(또는 그 반대로) 변환할 수 없습니다.

> [!NOTE]
> 대부분의 경우 변경 피드에서 읽어야 할 경우 가장 간단한 방법은 [변경 피드 프로세서](change-feed-processor.md)를 사용 하는 것입니다.

다음 시나리오에서는 끌어오기 모델 사용을 고려해야 합니다.

- 특정 파티션 키에서 변경 내용 읽기
- 클라이언트에서 처리를 위해 변경 내용을 받는 속도 제어
- 변경 피드의 기존 데이터를 한 번 읽습니다 (예: 데이터 마이그레이션을 수행 하는 경우).

변경 피드 프로세서와 끌어오기 모델의 주요 차이는 다음과 같습니다.

|기능  | 변경 피드 프로세서| 끌어오기 모델 |
| --- | --- | --- |
| 처리 중인 변경 피드의 현재 지점 추적 | 임대(Azure Cosmos DB 컨테이너에 저장됨) | 연속 토큰(메모리에 저장되거나 수동으로 유지됨) |
| 이전 변경 내용 재생 가능 여부 | 예, 밀어넣기 모델 사용 | 예, 끌어오기 모델 사용|
| 이후 변경 내용 폴링 | 사용자 지정 `WithPollInterval`에 기반하여 변경 내용을 자동으로 확인 | 설명서 |
| 새 변경 내용이 없는 동작 | 자동으로 대기 `WithPollInterval` 및 다시 확인 | 예외를 catch 하 고 수동으로 다시 확인 해야 합니다. |
| 전체 컨테이너의 변경 내용 처리 | 예, 동일한 컨테이너에서 사용하는 여러 스레드/머신을 자동으로 병렬화| 예, FeedToken을 사용하여 수동으로 병렬화 |
| 단일 파티션 키의 변경 내용 처리 | 지원되지 않음 | 예|
| 지원 수준 | 일반 공급 | 미리 보기 |

> [!NOTE]
> 변경 피드 프로세서를 사용 하 여 읽는 경우와 달리 새로운 변경 내용이 없는 경우를 명시적으로 처리 해야 합니다. 

## <a name="consuming-an-entire-containers-changes"></a>전체 컨테이너의 변경 내용 사용

끌어오기 모델을 사용하여 변경 피드를 처리하는 `FeedIterator`를 만들 수 있습니다. 를 처음 만들 때 `FeedIterator` `ChangeFeedStartFrom` 변경 내용 읽기의 시작 위치와 원하는를 모두 구성 하는 데 필요한 값을 지정 해야 합니다 `FeedRange` . 는 `FeedRange` 파티션 키 값의 범위 이며 특정을 사용 하 여 변경 피드에서 읽어올 항목을 지정 합니다 `FeedIterator` .

선택적으로를 지정 `ChangeFeedRequestOptions` 하 여를 설정할 수 있습니다 `PageSizeHint` . 는 `PageSizeHint` 단일 페이지에서 반환 되는 최대 항목 수입니다.

`FeedIterator`는 두 가지 버전으로 제공됩니다. 엔터티 개체를 반환하는 아래 예제 외에, `Stream` 지원을 통해 응답을 얻을 수도 있습니다. 스트림을 사용하면 데이터를 먼저 역직렬화하지 않고도 읽을 수 있어서, 클라이언트 리소스를 절약할 수 있습니다.

엔터티 개체(이 경우, `User` 개체)를 반환하는 `FeedIterator`를 가져오는 예는 다음과 같습니다.

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

`Stream`을 반환하는 `FeedIterator`를 가져오는 예는 다음과 같습니다.

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

에를 제공 하지 않는 경우 `FeedRange` `FeedIterator` 고유한 속도로 전체 컨테이너의 변경 피드를 처리할 수 있습니다. 다음은 현재 시간에서 시작 하는 모든 변경 내용 읽기를 시작 하는 예제입니다.

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

변경 피드는 실제로 모든 쓰기 및 업데이트를 포괄 하는 항목의 무한 목록이 기 때문에의 값은 `HasMoreResults` 항상 true입니다. 변경 피드를 읽으려고 할 때 새 변경 내용을 사용할 수 없으면 예외가 발생 합니다. 위의 예제에서 예외는 변경에 대해 rechecking 하기 전에 5 초 동안 대기 하 여 처리 됩니다.

## <a name="consuming-a-partition-keys-changes"></a>파티션 키의 변경 내용 사용

특정 파티션 키의 변경 내용만 처리하려는 경우가 있습니다. 특정 파티션 키에 대한 `FeedIterator`를 가져와서 전체 컨테이너와 같은 방식으로 변경 내용을 처리할 수 있습니다.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>병렬 처리에 FeedRange 사용

[변경 피드 프로세서](change-feed-processor.md)에서 작업은 여러 소비자에게 자동으로 분산됩니다. 변경 피드 끌어오기 모델에서 `FeedRange`를 사용하여 변경 피드에 대한 처리를 병렬화할 수 있습니다. `FeedRange`는 파티션 키 값의 범위를 나타냅니다.

컨테이너에 대한 범위 목록을 가져오는 방법을 보여주는 예는 다음과 같습니다.

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

컨테이너에 대한 FeedRanges 목록을 가져올 때 [실제 파티션](partitioning-overview.md#physical-partitions)당 하나의 `FeedRange`를 가져옵니다.

`FeedRange`를 사용하면 여러 머신 또는 스레드의 변경 피드 처리를 병렬화하는 `FeedIterator`를 만들 수 있습니다. 전체 컨테이너 또는 단일 파티션 키에 대해를 가져오는 방법을 보여 준 이전 예제와는 달리 `FeedIterator` FeedRanges를 사용 하 여 변경 피드를 병렬로 처리할 수 있는 여러 FeedIterators 얻을 수 있습니다.

FeedRanges를 사용하려는 경우에는 FeedRanges를 가져와서 해당 머신에 배포하는 오케스트레이터 프로세스가 필요합니다. 이러한 배포는 다음과 같을 수 있습니다.

* `FeedRange.ToJsonString`을 사용하고 이 문자열 값을 배포합니다. 소비자는 이 값을 `FeedRange.FromJsonString`과 함께 사용할 수 있습니다.
* 배포를 처리 중이면 `FeedRange` 개체 참조를 전달하는 것입니다.

다음은 병렬로 읽는 개별 가상 머신 두 대를 사용하여 컨테이너의 변경 피드 시작 부분부터 읽는 방법을 보여주는 샘플입니다.

머신 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

머신 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>연속 토큰 저장

연속 토큰을 생성하여 `FeedIterator`의 위치를 저장할 수 있습니다. 연속 토큰은 FeedIterator가 마지막으로 처리한 변경 내용의 지점을 추적하는 문자열 값입니다. 이를 통해, 나중에 해당 지점에서 `FeedIterator`를 재개할 수 있습니다. 다음은 컨테이너를 만든 이후의 변경 피드를 읽는 코드입니다. 변경 내용이 더 이상 없으면 연속 토큰을 유지하여 변경 피드 사용을 나중에 재개할 수 있도록 합니다.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

Cosmos 컨테이너가 여전히 존재하는 한 FeedIterator의 연속 토큰은 만료되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [변경 피드 개요](change-feed.md)
* [변경 피드 프로세서 사용](change-feed-processor.md)
* [Azure Functions 트리거](change-feed-functions.md)