---
title: 구독 간에 얼굴 데이터 마이그레이션 - Face API
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 한 Face API 구독에서 다른 구독으로 저장된 얼굴 데이터를 마이그레이션하는 방법을 보여 줍니다.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 02e9b64c89eda1471d644e0116bbf8c1c061ccc3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682528"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>얼굴 데이터를 다른 Face 구독으로 마이그레이션

이 가이드에서는 스냅숏 기능을 사용하여 얼굴 데이터(예: 저장된 얼굴의 **PersonGroup**)를 다른 Face API 구독으로 이동하는 방법을 보여 줍니다. 이렇게 하면 작업을 이동하거나 확장할 때 **PersonGroup** 또는 **FaceList**를 반복해서 구축하고 학습할 필요가 없습니다. 예를 들어, 평가판 구독을 사용하여 **PersonGroup**을 만들었으며, 현재 유료 구독으로 마이그레이션하려고 하거나 대규모 엔터프라이즈 작업을 위해 지역 간에 얼굴 데이터를 동기화해야 할 수 있습니다.

이러한 동일한 마이그레이션 전략이 **LargePersonGroup** 및 **LargeFaceList** 개체에도 적용됩니다. 이 가이드의 개념을 잘 모르는 경우 [얼굴 인식 개념](../concepts/face-recognition.md) 가이드에서 해당 정의를 참조합니다. 이 가이드에서는 C#에서 Face API .NET 클라이언트 라이브러리를 사용합니다.

## <a name="prerequisites"></a>필수 조건

- 두 Face API 구독 키(기존 데이터를 포함하는 키와 마이그레이션할 대상 키). [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- 대상 구독에 해당하는 Face API 구독 ID 문자열(Azure Portal의 **개요** 블레이드에 있음) 
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

이 가이드에서는 간단한 콘솔 앱을 사용하여 얼굴 데이터 마이그레이션을 실행합니다. 전체 구현에 대해서는 GitHub에서 [Face API 스냅숏 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)을 참조하세요.

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **FaceApiSnapshotSample**로 이름을 지정합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭을 클릭하고 **시험판 포함**을 선택한 후, 다음 패키지를 설치합니다.
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>얼굴 클라이언트 만들기

*Program.cs*의 **Main** 메서드에서 원본 및 대상 구독을 위한 2개의 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 를 만듭니다. 이 예제에서는 동아시아 지역의 Face 구독을 원본으로, 미국 서부 구독을 대상으로 사용합니다. 여기서는 한 Azure 지역에서 다른 지역으로 데이터를 마이그레이션하는 방법을 보여 줍니다. 구독이 다른 지역에 있으면 `Endpoint` 문자열을 변경해야 합니다.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

원본 및 대상 구독의 구독 키 값과 엔드포인트 URL을 입력해야 합니다.


## <a name="prepare-a-persongroup-for-migration"></a>PersonGroup 마이그레이션 준비

대상 구독으로 마이그레이션하려면 원본 구독의 **PersonGroup** ID를 알아야 합니다. **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** 메서드를 사용하여 **PersonGroup** 개체 목록을 검색한 후 **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** 속성을 가져옵니다. 이 프로세스는 보유하고 있는 **PersonGroup** 개체에 따라 다르게 표시됩니다. 이 가이드에서는 원본 **PersonGroup** ID가 `personGroupId`에 저장됩니다.

> [!NOTE]
> [샘플 코드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)는 마이그레이션할 새 **PersonGroup**을 만들고 학습하지만, 대부분의 경우 사용할 **PersonGroup**이 이미 있습니다.

## <a name="take-snapshot-of-persongroup"></a>PersonGroup 스냅숏 만들기

스냅숏은 특정 얼굴 데이터 형식에 대한 임시 원격 스토리지입니다. 스냅숏은 다른 구독 간에 데이터를 복사하기 위해 클립보드의 일종으로 작동합니다. 먼저 사용자는 원본 구독에서 데이터의 스냅숏을 “촬영”한 다음, 대상 구독에서 새 데이터 개체에 “적용”합니다.

원본 구독의 **FaceClient** 인스턴스를 사용하고 **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** 와 **PersonGroup** ID 및 대상 구독 ID를 지정하여 **PersonGroup**의 스냅숏을 만듭니다. 대상 구독이 여러 개 있는 경우 세 번째 매개 변수에 배열 항목으로 추가할 수 있습니다.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 스냅숏을 만들고 적용하는 프로세스는 원본 또는 대상 **PersonGroup**(또는 **FaceList**)에 대한 정규 호출을 방해하지 않습니다. 그러나, 원본 개체를 변경하는 동시 호출(예를 들어 [FaceList management](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) 또는 [PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) 호출)을 권장하지는 않습니다, 왜냐하면 스냅숏 작업은 이러한 작업 전후 실행되거나 오류가 발생할 수 있기 때문입니다.

## <a name="retrieve-the-snapshot-id"></a>스냅숏 ID 검색

스냅숏을 찍는 메서드는 비동기식이므로 완료될 때까지 기다려야 합니다(스냅숏 작업은 취소할 수 없음). 이 코드에서 WaitForOperation 메서드는 비동기 호출을 모니터링하여 100ms마다 상태를 확인합니다. 작업이 완료되면 작업 ID를 검색할 수 있습니다. `OperationLocation` 필드를 구문 분석하여 작업 ID를 가져올 수 있습니다.

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

일반적인 `OperationLocation` 값은 다음과 같습니다.

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

여기에 `WaitForOperation` 도우미 메서드가 있습니다.

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

작업 상태가 `Succeeded`로 표시되면 반환된 **OperationStatus** 인스턴스의 `ResourceLocation` 필드를 구문 분석하여 스냅숏 ID를 가져올 수 있습니다.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

일반적인 `resourceLocation` 값은 다음과 같습니다.

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>대상 구독에 스냅숏 적용

다음으로 무작위로 생성된 ID를 사용하여 대상 구독에 새 **PersonGroup**을 만듭니다. 그런 후 대상 구독의 **FaceClient** 인스턴스를 사용하여 이 PersonGroup에 스냅숏을 적용하고 스냅숏 ID 및 새 **PersonGroup** ID를 제공합니다. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 스냅숏 개체는 48시간 동안만 유효합니다. 바로 데이터 마이그레이션에 사용하려는 경우에만 스냅숏을 만드는 것이 좋습니다.

스냅숏 적용 요청은 다른 작업 ID를 반환합니다. 반환된 **applySnapshotResult** 인스턴스의 `OperationLocation` 필드를 구문 분석하여 이 ID를 가져올 수 있습니다. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

또한 스냅숏 애플리케이션 프로세스가 비동기적이므로 `WaitForOperation`을 다시 사용하여 완료될 때까지 기다립니다.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>데이터 마이그레이션 테스트

스냅숏을 적용한 후에 대상 구독의 새 **PersonGroup**이 원래 얼굴 데이터로 채워져야 합니다. 기본적으로 학습 결과도 복사되므로 새 **PersonGroup**은 재학습 없이도 얼굴 식별 호출에 사용할 수 있는 상태가 됩니다.

데이터 마이그레이션을 테스트하려면 다음 작업을 실행하고 콘솔에 출력하는 결과를 비교할 수 있습니다.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

다음과 같은 도우미 메서드를 사용합니다.

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

이제 대상 구독에서 새 **PersonGroup**을 사용할 수 있습니다. 

향후에 대상 **PersonGroup**을 업데이트할 예정이면 이 가이드의 단계에 따라 새 **PersonGroup**을 만들어 스냅숏을 수신해야 합니다. 단일 **PersonGroup** 개체에는 한 번에 하나의 스냅숏만 적용될 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

얼굴 데이터 마이그레이션 작업이 끝나면 스냅숏 개체를 수동으로 삭제하는 것이 좋습니다.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>다음 단계

다음으로, 관련 API 참조 설명서를 참조하여 스냅숏 기능을 사용하는 샘플 앱을 탐색하거나 여기 언급된 다른 API 작업을 사용하여 시작하는 방법에 대한 지침을 따릅니다.

- [스냅숏 참조 설명서(.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Face API 스냅숏 예제](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [얼굴 추가 방법](how-to-add-faces.md)
- [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)
- [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)
