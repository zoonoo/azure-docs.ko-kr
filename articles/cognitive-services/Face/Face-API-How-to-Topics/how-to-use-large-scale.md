---
title: '예제: 대규모 기능 사용 - Face API'
titleSuffix: Azure Cognitive Services
description: Face API에서 대규모 기능을 사용합니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229840"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>예제: 대규모 기능을 사용하는 방법

이 가이드는 기존 **PersonGroup** 및 **FaceList**에서 각각 **LargePersonGroup** 및 **LargeFaceList**로 강화하는 방법에 대한 고급 문서입니다. 이 가이드에서는 마이그레이션 프로세스를 보여주며, 여러분에게 **PersonGroup**, **FaceList**, [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) 작업 및 얼굴 인식 기능에 대한 기본 지식이 있다고 가정합니다. 자세한 내용은 [얼굴 인식](../concepts/face-recognition.md) 개념 가이드를 참조하세요.

LargePersonGroup과 LargeFaceList를 합쳐서 대규모 작업이라고 부릅니다. LargePersonGroup은 최대 1,000,000명의 사람과 248개의 얼굴을 포함할 수 있고, LargeFaceList는 최대 1,000,000개의 얼굴을 포함할 수 있습니다. 대규모 작업은 기존의 PersonGroup 및 FaceList와 비슷하지만 새 아키텍처 때문에 몇 가지 뚜렷한 차이가 있습니다. 

샘플은 Face API 클라이언트 라이브러리를 사용하여 C#에서 작성되었습니다.

> [!NOTE]
> 대규모로 Identification 및 FindSimilar에 대해 얼굴 검색 성능을 활성화하려면 LargeFaceList 및 LargePersonGroup을 전처리하도록 학습 작업을 도입해야 합니다. 학습 시간은 실제 용량에 따라 초 단위에서 약 30분으로 다릅니다. 학습 기간 동안 성공적인 학습이 이전에 수행된 경우 Identification 및 FindSimilar를 계속해서 수행할 수 있습니다. 그러나 단점은 대규모 학습으로 새 게시 마이그레이션이 완료될 때까지 새로 추가된 사용자/얼굴이 결과에 표시되지 않는 것입니다.

## <a name="step-1-initialize-the-client-object"></a>1단계: 클라이언트 개체 초기화

Face API 클라이언트 라이브러리를 사용할 때 FaceServiceClient 클래스의 생성자를 통해 구독 키 및 구독 엔드포인트가 전달됩니다. 예: 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

해당 엔드포인트가 있는 구독 키는 Azure Portal의 Marketplace 페이지에서 얻을 수 있습니다.
[구독](https://azure.microsoft.com/services/cognitive-services/directory/vision/)을 참조하세요.

## <a name="step-2-code-migration"></a>2단계: 코드 마이그레이션

이 섹션은 LargePersonGroup/LargeFaceList로 PersonGroup/FaceList 구현 마이그레이션에 중점을 둡니다. LargePersonGroup/LargeFaceList는 디자인 및 내부 구현에서 PersonGroup/FaceList와 다르지만, API 인터페이스가 이전 버전과 호환된다는 점은 비슷합니다.

데이터 마이그레이션은 지원되지 않으므로 LargePersonGroup/LargeFaceList를 대신 다시 만들어야 합니다.

### <a name="migrate-persongroup-to-largepersongroup"></a>LargePersonGroup으로 PersonGroup 마이그레이션

PersonGroup과 LargePersonGroup이 정확하게 동일한 그룹 수준 작업을 공유하기 때문에 PersonGroup에서 LargePersonGroup으로의 마이그레이션은 정말 간단합니다.

PersonGroup/Person 관련 구현의 경우 API 경로 또는 SDK 클래스/모듈을 LargePersonGroup 및 LargePersonGroup Person으로 변경하면 됩니다.

PersonGroup의 모든 얼굴과 사람을 새 LargePersonGroup에 추가해야 합니다. [얼굴 추가 방법](how-to-add-faces.md)을 참조하세요.

### <a name="migrate-facelist-to-largefacelist"></a>LargeFaceList로 FaceList 마이그레이션

| FaceList API | LargeFaceList API |
|:---:|:---:|
| 생성 | 생성 |
| 삭제 | 삭제 |
| 가져오기 | 가져오기 |
| 나열 | 나열 |
| 주 지역에서 | 주 지역에서 |
| - | 학습 |
| - | 학습 상태 가져오기 |

앞의 테이블은 FaceList와 LargeFaceList 간 목록 수준 작업의 비교입니다. 표시된 대로 FaceList와 비교했을 때 LargeFaceList는 새 작업, 학습 및 학습 상태 가져오기와 함께 제공됩니다. 학습된 LargeFaceList를 가져오는 것은 FaceList에 필요한 학습이 없으므로 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업의 전제 조건입니다. 다음 코드 조각은 LargeFaceList의 학습에 대해 대기하는 도우미 함수입니다.

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

이전에 얼굴을 추가하는 FaceList의 일반적인 사용법과 FindSimilar는 다음과 같습니다.

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

LargeFaceList로 마이그레이션하는 경우 다음과 같이 되어야 합니다.

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

위와 같이 데이터 관리 및 FindSimilar 파트는 거의 동일합니다. 유일한 예외는 FindSimilar가 작동하기 전에 LargeFaceList에서 전처리 학습 작업 새로 고치기를 완료해야 하는 것입니다.

## <a name="step-3-train-suggestions"></a>3단계: 학습 제안 사항

학습 작업은 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 및 [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)의 속도를 향상시키지만 특히 대규모인 경우 학습 시간이 저하됩니다. 다음 테이블에 다른 규모의 예상되는 학습 시간이 나열됩니다.

| 규모(얼굴 또는 사용자) | 예상되는 학습 시간 |
|:---:|:---:|
| 1,000 | 1-2초 |
| 10000 | 5-10초 |
| 100,000 | 1-2분 |
| 1,000,000 | 10-30분 |

대규모 기능을 더 잘 활용하려면 몇 가지 전략을 고려하는 것이 좋습니다.

## <a name="step-31-customize-time-interval"></a>3.1단계: 시간 간격 사용자 지정

`TrainLargeFaceList()`에 표시된 대로 무한 학습 상태 확인 프로세스를 지연하는 `timeIntervalInMilliseconds`가 있습니다. 더 많은 얼굴이 있는 LargeFaceList의 경우 큰 간격을 사용하면 호출 수 및 비용을 감소시킵니다. LargeFaceList의 예상되는 용량에 따라 시간 간격을 사용자 지정해야 합니다.

동일한 전략이 LargePersonGroup에도 적용됩니다. 예를 들어 한 1,000,000명의 사용자가 있는 LargePersonGroup을 학습하는 경우 `timeIntervalInMilliseconds`는 60,000(1분 간격)이 될 수 있습니다.

## <a name="step-32-small-scale-buffer"></a>3.2단계: 소규모 버퍼

LargePersonGroup/LargeFaceList의 사용자/얼굴은 학습된 후에 검색할 수 있습니다. 동적 시나리오에서 새 사용자/얼굴이 지속적으로 추가되고 즉시 검색할 수 있어야 합니다. 학습은 원하는 것보다 오래 걸릴 수 있습니다. 이 문제를 완화하기 위해 소규모 추가 LargePersonGroup/LargeFaceList를 버퍼로 새로 추가된 항목에 대해서만 사용할 수 있습니다. 이 버퍼는 훨씬 작은 크기로 인해 학습하는 데 더 짧은 시간이 소요되며 이 임시 버퍼에서 즉시 검색이 작동되어야 합니다. 더욱 드문 간격으로(예: 자정에 매일) 마스터 학습을 실행하여 마스터 LargePersonGroup/LargeFaceList에서 학습과 조합하여 이 버퍼를 사용합니다.

예제 워크플로:
1. 마스터 LargePersonGroup/LargeFaceList(마스터 컬렉션) 및 버퍼 LargePersonGroup/LargeFaceList(버퍼 컬렉션)를 만듭니다. 버퍼 컬렉션은 새로 추가된 사용자/얼굴용입니다.
1. 마스터 컬렉션 및 버퍼 컬렉션 모두에 새 사용자/얼굴을 추가합니다.
1. 새로 추가된 항목을 적용하도록 짧은 시간 간격으로 버퍼 컬렉션을 학습합니다.
1. 마스터 컬렉션 및 버퍼 컬렉션 모두에 대해 Identification/FindSimilar를 호출하고, 결과를 병합합니다.
1. 버퍼 컬렉션 크기가 임계값으로 또는 시스템 유휴 시간에서 증가하는 경우 새 버퍼 컬렉션을 만들고 마스터 컬렉션에 대한 학습을 트리거합니다.
1. 마스터 컬렉션에 대한 학습을 완료한 후 이전 버퍼 컬렉션을 삭제합니다.

## <a name="step-33-standalone-training"></a>3.3단계: 독립 실행형 학습

상대적으로 긴 대기 시간이 허용되는 경우 새 데이터를 추가한 후 바로 학습 작업을 트리거할 필요가 없습니다. 대신 학습 작업은 기본 논리에서 분할되고 정기적으로 트리거될 수 있습니다. 이 전략은 허용 가능한 대기 시간을 사용하는 동적 시나리오에 적합하며, 학습 빈도를 더 줄이기 위해 정적 시나리오에 적용할 수 있습니다.

`TrainLargeFaceList`와 유사한 `TrainLargePersonGroup` 기능이 있다고 가정합니다. `System.Timers`에서 [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) 클래스를 호출하여 LargePersonGroup에 대한 독립 실행형 학습의 일반적인 구현은 다음과 같습니다.

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

데이터 관리 및 식별 관련 구현에 대한 자세한 내용은 [얼굴을 추가하는 방법](how-to-add-faces.md) 및 [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)을 참조하세요.

## <a name="summary"></a>요약

이 가이드에서는 기존 PersonGroup/FaceList 코드(데이터가 아닌)를 LargePersonGroup/LargeFaceList로 마이그레이션하는 방법을 배웠습니다.

- LargePersonGroup 및 LargeFaceList는 LargeFaceList에 학습 작업이 필요한 것을 제외하고 PersonGroup/FaceList와 유사하게 작동합니다.
- 대규모 데이터 세트에 대한 동적 데이터 업데이트에 적절한 학습 전략을 사용합니다.

## <a name="next-steps"></a>다음 단계

방법 가이드에 따라 PersonGroup에 얼굴을 추가하거나 PersonGroup에 대한 확인 작업을 실행하는 방법을 알아봅니다.

- [얼굴을 추가하는 방법](how-to-add-faces.md)
- [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)