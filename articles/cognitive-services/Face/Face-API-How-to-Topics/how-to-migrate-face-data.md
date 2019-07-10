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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65913794"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>얼굴 데이터를 다른 Face 구독으로 마이그레이션

이 가이드에서는 얼굴을 포함 하는 저장 된 PersonGroup 개체 등의 얼굴 데이터를 다른 Azure Cognitive Services Face API 구독으로 이동 하는 방법을 보여 줍니다. 데이터를 이동 하려면 스냅숏 기능을 사용 합니다. 이러한 방식으로 않아도 반복적으로 빌드하고 학습 PersonGroup 또는 FaceList 개체를 이동 하거나 작업을 확장 합니다. 예를 들어, 아마도 PersonGroup 개체 무료 평가판 구독을 사용 하 여 만들고 이제 유료 구독에 마이그레이션해야 합니다. 또는 대규모 엔터프라이즈 작업에 대 한 지역에 걸쳐 얼굴 데이터를 동기화 해야 할 수 있습니다.

이 동일한 마이그레이션 전략 LargePersonGroup 및 LargeFaceList 개체에도 적용 됩니다. 이 가이드의 개념을 잘 모르는 경우 해당 정의 참조 합니다 [얼굴 인식 개념](../concepts/face-recognition.md) 가이드입니다. 이 가이드에서는 C#에서 Face API .NET 클라이언트 라이브러리를 사용합니다.

## <a name="prerequisites"></a>필수 조건

다음 항목이 필요합니다.

- 2 개의 Face API 구독 키를 기존 데이터를 사용 하 여 하나 및로 마이그레이션하는 것입니다. Face API 서비스를 구독할를 넌 트 키를 가져오려면의 지침을 따릅니다 [Cognitive Services 계정을 만드는](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)합니다.
- 대상 구독에 해당 하는 Face API 구독 ID 문자열입니다. 선택 찾으려면 **개요** Azure portal에서 합니다. 
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

이 가이드 얼굴 데이터 마이그레이션을 실행 하는 간단한 콘솔 앱을 사용 합니다. 전체 구현을 참조 합니다 [Face API 스냅숏 예제](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) GitHub에서.

1. Visual Studio에서 새 콘솔 앱.NET Framework 프로젝트를 만듭니다. 이름을 **FaceApiSnapshotSample**합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 누르고 **NuGet 패키지 관리**합니다. 선택 된 **찾아보기** 탭을 선택한 **시험판 포함**합니다. 찾기 및 다음 패키지를 설치 합니다.
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>얼굴 클라이언트 만들기

에 **Main** 에서 메서드 *Program.cs*, 2 개를 만든 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 원본 및 대상 구독에 대 한 인스턴스. 이 예제에서는 원본 및 대상으로 미국 서 부 구독 동아시아 지역에서 얼굴 구독을 사용합니다. 이 예제에서는 다른 Azure 지역 간에 데이터를 마이그레이션하는 방법을 보여 줍니다. 구독의 다른 지역에 있는 경우 변경 된 `Endpoint` 문자열입니다.

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

구독 키 값과 원본 및 대상 구독에 대 한 끝점 Url을 입력 합니다.


## <a name="prepare-a-persongroup-for-migration"></a>PersonGroup 마이그레이션 준비

대상 구독으로 마이그레이션하려면 원본 구독에는 PersonGroup의 ID가 필요 합니다. 사용 된 [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) PersonGroup 개체의 목록을 검색 하는 방법입니다. 그런 다음 가져올는 [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) 속성입니다. 이 프로세스는 PersonGroup에 따라 다르게 보이므로 있는 개체 수입니다. 이 가이드에서는 PersonGroup ID 원본에 저장 된 `personGroupId`합니다.

> [!NOTE]
> 합니다 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) 만들고 마이그레이션할 새 PersonGroup 학습 합니다. 대부분의 경우에서 이미 사용 하는 PersonGroup 해야 합니다.

## <a name="take-a-snapshot-of-a-persongroup"></a>PersonGroup의 스냅숏 만들기

스냅숏은 특정 얼굴 데이터 형식에 대 한 임시 원격 저장소입니다. 스냅숏은 다른 구독 간에 데이터를 복사하려면 클립보드의 일종으로 작동합니다. 먼저 이전 구독에서 데이터의 스냅숏을 만듭니다. 그런 다음 적용할 있습니다 대상 구독에 새 데이터 개체입니다.

PersonGroup의 스냅숏을 만들려면 원본 구독의 FaceClient 인스턴스를 사용 합니다. 사용 하 여 [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) PersonGroup ID 및 대상 구독 id입니다. 대상 구독이 여러 개 있는 경우 세 번째 매개 변수 배열 항목으로 추가 합니다.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 수행 하 고 스냅숏 적용 프로세스를 원본에 대 한 모든 일반 호출을 방해 하지 않습니다 또는 PersonGroups 또는 FaceLists 대상입니다. 와 같은 원본 개체를 변경 하는 동시 호출을 수행 하지 마세요 [FaceList 관리 호출](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) 또는 [PersonGroup 학습](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) 를 예를 들어 호출 합니다. 스냅숏 작업이 해당 작업 전후에 실행 될 수 또는 오류가 발생할 수 있습니다.

## <a name="retrieve-the-snapshot-id"></a>스냅숏 ID를 검색 합니다.

스냅숏을 만드는 데 사용할 메서드를 비동기 이므로 해당 완료 대기 해야 합니다. 스냅숏 작업을 취소할 수 없습니다. 이 코드는 `WaitForOperation` 메서드 비동기 호출을 모니터링 합니다. 100 밀리초 마다 상태를 확인합니다. 작업이 완료 되 면 구문 분석 하 여 작업 ID를 검색 합니다 `OperationLocation` 필드입니다. 

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

작업 상태를 한 후 표시 `Succeeded`, 구문 분석 하 여 스냅숏 ID를 가져옵니다는 `ResourceLocation` 반환 된 OperationStatus 인스턴스 필드입니다.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

일반적인 `resourceLocation` 값은 다음과 같습니다.

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>대상 구독에 스냅숏을 적용합니다

다음으로 임의로 생성 된 ID를 사용 하 여 대상 구독에 새 PersonGroup를 만듭니다 그런 다음이 PersonGroup에 스냅숏을 적용 하려면 대상 구독의 FaceClient 인스턴스를 사용 합니다. 스냅숏 ID와 새 PersonGroup ID 전달

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 스냅숏 개체 48 시간 동안만 유효합니다. 데이터 마이그레이션에 사용 하려는 경우에 스냅숏의 만드는 직후입니다.

스냅숏 적용 요청을 다른 작업 ID를 반환합니다. 이 ID를 가져오려면 구문 분석 된 `OperationLocation` 반환된 applySnapshotResult 인스턴스 필드입니다. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

또한 비동기 스냅숏 응용 프로그램 프로세스에 다시 사용 하 여 `WaitForOperation` 완료 될 때까지 기다려야 합니다.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>데이터 마이그레이션 테스트

스냅숏을 적용 한 후 대상 구독에 새 PersonGroup 원래 얼굴 데이터를 채웁니다. 기본적으로 학습 결과 복사 됩니다. 재 학습 하지 않고도 새 PersonGroup 얼굴 식별 호출에 대 한 준비가 되었습니다.

데이터 마이그레이션을 테스트 하려면 다음 작업을 실행 하 고 콘솔에 출력 결과 비교 합니다.

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

이제 대상 구독에 새 PersonGroup를 사용할 수 있습니다. 

나중에 다시 대상 PersonGroup 업데이트 하려면 스냅숏을 받으려면 새 PersonGroup를 만듭니다. 이 위해이 가이드의 단계를 따릅니다. 단일 PersonGroup 개체를 한 번만 적용 스냅숏을 가질 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

마이그레이션한 후 데이터 얼굴, 스냅숏 개체를 수동으로 삭제 합니다.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>다음 단계

다음으로, 관련 API 참조 설명서를 참조, 스냅숏 기능을 사용 하는 샘플 앱을 탐색 하거나 여기 언급 된 다른 API 작업을 사용 하 여 시작 하는 방법 지침을 따릅니다.

- [스냅숏 참조 설명서(.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Face API 스냅숏 예제](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [얼굴 추가](how-to-add-faces.md)
- [이미지에서 얼굴 감지](HowtoDetectFacesinImage.md)
- [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)
