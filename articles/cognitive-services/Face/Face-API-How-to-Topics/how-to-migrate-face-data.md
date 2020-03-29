---
title: 구독 간에 얼굴 데이터 마이그레이션 - Face
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 저장된 얼굴 데이터를 한 Face 구독에서 다른 얼굴 구독으로 마이그레이션하는 방법을 보여 줍니다.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169805"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>얼굴 데이터를 다른 Face 구독으로 마이그레이션

이 가이드에서는 얼굴이 있는 저장된 PersonGroup 개체와 같은 얼굴 데이터를 다른 Azure Cognitive Services Face 구독으로 이동하는 방법을 보여 주었습니다. 데이터를 이동하려면 스냅샷 기능을 사용합니다. 이렇게 하면 작업을 이동하거나 확장할 때 PersonGroup 또는 FaceList 개체를 반복적으로 빌드하고 학습하지 않아도 됩니다. 예를 들어 무료 평가판 구독을 사용하여 PersonGroup 개체를 만들었으며 이제 유료 구독으로 마이그레이션하려고 할 수 있습니다. 또는 대기업 운영을 위해 여러 지역의 구독 간에 얼굴 데이터를 동기화해야 할 수 있습니다.

이러한 동일한 마이그레이션 전략이 LargePersonGroup 및 LargeFaceList 개체에도 적용됩니다. 이 가이드의 개념에 익숙하지 않은 경우 얼굴 인식 개념 가이드에서 해당 [정의를 참조하세요.](../concepts/face-recognition.md) 이 가이드에서는 C#을 사용하여 Face .NET 클라이언트 라이브러리를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 항목이 필요합니다.

- 두 개의 Face 구독 키( 하나는 기존 데이터와 마이그레이션할 키)입니다. Face 서비스에 가입하고 키를 얻으려면 [인지 서비스 계정 만들기의](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)지침을 따르십시오.
- 대상 구독에 해당하는 Face 구독 ID 문자열입니다. 이를 찾으려면 Azure 포털에서 **개요를** 선택합니다. 
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

이 가이드에서는 간단한 콘솔 앱을 사용하여 얼굴 데이터 마이그레이션을 실행합니다. 전체 구현은 GitHub의 [Face 스냅샷 샘플을](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) 참조하십시오.

1. Visual Studio에서 새 콘솔 앱 .NET Framework 프로젝트를 만듭니다. **FaceApiSnapshotSample.**
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리를 선택합니다.** **찾아보기** 탭을 선택하고 **시험판 포함을**선택합니다. 다음 패키지를 찾아 설치합니다.
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>얼굴 클라이언트 만들기

*Program.cs*의 **Main** 메서드에서 원본 및 대상 구독을 위한 2개의 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)를 만듭니다. 이 예제에서는 동아시아 지역의 Face 구독을 소스로 사용하고 미국 서부 구독을 대상으로 사용합니다. 이 예제에서는 한 Azure 리전에서 다른 Azure 리전으로 데이터를 마이그레이션하는 방법을 보여 줍니다. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

소스 및 대상 구독에 대한 구독 키 값과 끝점 URL을 입력합니다.


## <a name="prepare-a-persongroup-for-migration"></a>PersonGroup 마이그레이션 준비

대상 구독으로 마이그레이션하려면 원본 구독의 PersonGroup ID를 알아야 합니다. [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) 메서드를 사용하여 PersonGroup 개체 목록을 검색합니다. 그런 다음 [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) 속성을 가져옵니다. 이 프로세스는 PersonGroup 개체에 따라 다르게 표시됩니다. 이 가이드에서는 원본 PersonGroup ID가 `personGroupId`에 저장됩니다.

> [!NOTE]
> [샘플 코드는](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) 마이그레이션할 새 PersonGroup을 만들고 훈련시보냅니다. 대부분의 경우 사용할 PersonGroup이 이미 있어야 합니다.

## <a name="take-a-snapshot-of-a-persongroup"></a>사람 그룹의 스냅샷 생성

스냅샷은 특정 Face 데이터 유형에 대한 임시 원격 저장소입니다. 스냅숏은 다른 구독 간에 데이터를 복사하려면 클립보드의 일종으로 작동합니다. 먼저 원본 구독에서 데이터의 스냅숏을 생성합니다. 그런 다음 대상 구독의 새 데이터 개체에 적용합니다.

소스 구독의 FaceClient 인스턴스를 사용하여 PersonGroup의 스냅숏을 생성합니다. PersonGroup ID 및 대상 구독ID와 함께 [TakeAsync를](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) 사용합니다. 대상 구독이 여러 개인 경우 세 번째 매개 변수의 배열 항목으로 추가합니다.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 스냅샷을 생성하고 적용하는 프로세스는 소스 또는 대상 PersonGroups 또는 FaceLists에 대한 정기적인 호출을 방해하지 않습니다. 예를 들어 [FaceList 관리](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) 호출 또는 [PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) 호출과 같이 소스 개체를 변경하는 동시 호출을 하지 마십시오. 스냅숏 작업은 이러한 작업 전후에 실행될 수 있으며 오류가 발생할 수 있습니다.

## <a name="retrieve-the-snapshot-id"></a>스냅샷 ID 검색

스냅숏을 찍는 데 사용되는 메서드는 비동기이므로 완료될 때까지 기다려야 합니다. 스냅샷 작업은 취소할 수 없습니다. 이 코드에서 `WaitForOperation` 메서드는 비동기 호출을 모니터링합니다. 100ms마다 상태를 확인합니다. 작업이 완료되면 필드를 구문 분석하여 작업 `OperationLocation` ID를 검색합니다. 

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

작업 상태가 표시되면 `Succeeded`반환된 OperationStatus 인스턴스의 `ResourceLocation` 필드를 구문 분석하여 스냅숏 ID를 가져옵니다.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

일반적인 `resourceLocation` 값은 다음과 같습니다.

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>대상 구독에 스냅샷 적용

다음으로 임의로 생성된 ID를 사용하여 대상 구독에서 새 PersonGroup을 만듭니다. 그런 다음 대상 구독의 FaceClient 인스턴스를 사용하여 이 PersonGroup에 스냅샷을 적용합니다. 스냅샷 ID와 새 PersonGroup ID를 전달합니다.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 스냅샷 개체는 48시간 동안만 유효합니다. 곧 데이터 마이그레이션에 사용하려는 경우에만 스냅숏을 생성합니다.

스냅샷 적용 요청은 다른 작업 ID를 반환합니다. 이 ID를 얻으려면 반환된 `OperationLocation` applySnapshotResult 인스턴스의 필드를 구문 분석합니다. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

스냅숏 응용 프로그램 프로세스도 비동기이므로 `WaitForOperation` 완료될 때까지 기다리는 데 다시 사용합니다.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>데이터 마이그레이션 테스트

스냅샷을 적용하면 대상 구독의 새 PersonGroup이 원래 얼굴 데이터로 채워집니다. 기본적으로 교육 결과도 복사됩니다. 새로운 PersonGroup은 재교육 없이 얼굴 식별 호출에 사용할 수 있습니다.

데이터 마이그레이션을 테스트하려면 다음 작업을 실행하고 인쇄한 결과를 콘솔과 비교합니다.

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

이제 대상 구독에서 새 PersonGroup을 사용할 수 있습니다. 

나중에 대상 PersonGroup을 다시 업데이트하려면 새 PersonGroup을 만들어 스냅샷을 수신합니다. 이렇게 하려면 이 가이드의 단계를 따르십시오. 단일 PersonGroup 개체에는 스냅숏이 한 번만 적용될 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

면 데이터 마이그레이션을 완료한 후 스냅샷 개체를 수동으로 삭제합니다.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>다음 단계

그런 다음 관련 API 참조 설명서를 참조하거나, 스냅샷 기능을 사용하는 샘플 앱을 탐색하거나, 여기에 언급된 다른 API 작업을 사용하는 방법 가이드를 따릅니다.

- [스냅샷 참조 설명서(.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [얼굴 스냅샷 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [얼굴 추가](how-to-add-faces.md)
- [이미지에서 얼굴 감지](HowtoDetectFacesinImage.md)
- [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)
