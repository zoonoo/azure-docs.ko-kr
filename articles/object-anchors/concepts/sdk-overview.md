---
title: 런타임 SDK 개요
description: 개체 앵커 런타임 SDK에 대해 알아봅니다.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 020f727674449523a57a608e8930d67e0f239cf6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746020"
---
# <a name="runtime-sdk-overview"></a>런타임 SDK 개요

이 섹션에서는 개체 앵커 모델을 사용 하 여 개체를 검색 하는 데 사용 되는 개체 앵커 런타임 SDK의 개략적인 개요를 제공 합니다. 개체가 표시 되는 방법 및 다양 한 구성 요소가 사용 되는 방법을 이해 하 게 됩니다.

아래에서 설명 하는 모든 형식은 **MixedReality** 네임 스페이스에서 찾을 수 있습니다.

## <a name="types"></a>유형

### <a name="objectmodel"></a>ObjectModel

[Objectmodel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) 은 물리적 개체의 기 하 도형을 나타내며 검색 및 포즈 예측에 필요한 매개 변수를 인코딩합니다. [개체 앵커 서비스](../quickstarts/get-started-model-ingestion.md)를 사용 하 여 만들어야 합니다. 그런 다음 응용 프로그램은 개체 앵커 API를 사용 하 여 생성 된 모델 파일을 로드 하 고 시각화를 위해 해당 모델에 포함 된 메시를 쿼리할 수 있습니다.

### <a name="objectsearcharea"></a>ObjectSearchArea

[Objectsearcharea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) 는 하나 또는 여러 개체를 찾을 수 있는 공간을 지정 합니다. 공간 그래프 노드 ID가 나타내는 좌표계의 공간 그래프 노드 ID 및 공간 범위에 의해 정의 됩니다. 개체 앵커 런타임 SDK는 네 가지 유형의 범위, 즉 **보기의 필드**, **경계 상자**, **구** 및 **위치** 를 지원 합니다.

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) 는 **개체 관찰자** 에 게 지정 된 모델의 개체를 찾는 방법을 알려 줍니다. 개체 모델에서 기본 값을 검색할 수 있는 다음 튜닝할 수 있는 매개 변수를 제공 합니다.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

[MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) 속성은 인스턴스를 검색 된 것으로 간주할 값을 나타냅니다.

**관찰자** 는 각 개체 후보에 대해 변환 된 개체 모델과 장면 간의 겹친 표면 비율을 계산 하 고, 검사 비율이 지정 된 임계값을 초과 하는 경우에만 응용 프로그램에 대 한 후보를 보고 합니다.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) 속성은 대상 개체가 그라운드 평면에 있을 것으로 예상 되는지 여부를 나타냅니다.

그라운드 평면은 검색 영역에서 가장 작은 가로 층입니다. 가능한 개체의 포즈에 대해 좋은 제약 조건을 제공 합니다. 이 플래그를 설정 하면 **관찰자** 가 제한 된 공간에서 포즈를 추정 하 고 정확도를 향상 시킬 수 있습니다. 모델이 그라운드 평면에서 사용할 필요가 없는 경우이 매개 변수는 무시 됩니다.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) 속성은 개체 인스턴스 및 중력의 위쪽 방향 사이에 예상 되는 최대 각도 (도)를 나타냅니다.

이 매개 변수는 예상 포즈의 위쪽 방향에 대 한 또 다른 제약 조건을 제공 합니다. 예를 들어 개체가 오른쪽에 있는 경우이 매개 변수는 0이 될 수 있습니다. 개체 앵커는 모델과 다른 개체를 검색 하지 않아야 합니다. 모델이 오른쪽에 있는 경우에는 해당 인스턴스를 검색 하지 않습니다. 새 모델은 하향 레이아웃에 사용 됩니다. Articulation에 동일한 규칙이 적용 됩니다.

#### <a name="maxscalechange"></a>MaxScaleChange

[MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) 속성은 공간 매핑과 관련 하 여 최대 개체 배율 변경 (0 ~ 1)을 나타냅니다. 예상 소수 자릿수가 원점에서 가운데 맞춤 된 개체 꼭지점과 축 정렬에 적용 됩니다. 예상 되는 배율은 CAD 모델과 실제 표현 사이의 실제 규모와는 다를 수 있지만, 앱이 물리적 개체의 공간 매핑과 가까운 개체 모델을 렌더링할 수 있는 일부 값입니다.

#### <a name="searchareas"></a>SearchAreas

[Searchareas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) 속성은 개체를 찾을 공간 경계 배열을 나타냅니다.

**관찰자** 는 쿼리에 지정 된 모든 검색 영역의 union 공간에서 개체를 찾습니다. 이 릴리스에서는 대기 시간을 줄이기 위해 신뢰도가 가장 높은 개체를 최대 하나만 반환 합니다.

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) 는 지정 된 모델의 인스턴스가 HoloLens 좌표계에 있을 수 있는 가상 위치를 나타냅니다. 각 인스턴스에는 `SurfaceCoverage` 예상 포즈의 올바른 정도를 나타내는 속성과 함께 제공 됩니다.

인스턴스는 메서드를 호출 하 여 만든 `ObjectObserver.DetectAsync` 다음, 활성 상태에서 백그라운드에서 자동으로 업데이트 됩니다. 응용 프로그램은 특정 인스턴스에서 상태 변경 이벤트를 수신 하거나 추적 모드를 변경 하 여 업데이트를 일시 중지/다시 시작할 수 있습니다. 인스턴스는 추적이 손실 될 때 **관찰자** 에서 자동으로 제거 됩니다.

### <a name="objectobserver"></a>ObjectObserver

[Objectobserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) 는 개체 모델을 로드 하 고, 해당 인스턴스를 검색 하 고, HoloLens 좌표계의 각 인스턴스에 대 한 6-DoF 포즈를 보고 합니다.

모든 개체 모델 또는 인스턴스는 **관찰자** 에서 만들어지므로 수명이 독립적입니다. 응용 프로그램은 관찰자를 삭제 하 고 개체 모델 또는 인스턴스를 계속 사용할 수 있습니다.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) 은 진단 정보를 기록 하 고 보관 파일에 데이터를 기록 합니다.

진단 보관에는 장면 지점 클라우드, 관찰자의 상태 및 모델에 대 한 정보가 포함 됩니다. 이 정보는 가능한 런타임 문제를 식별 하는 데 유용 합니다. 자세한 내용은 [FAQ](../faq.md)을 참조하세요.

## <a name="runtime-sdk-usage-and-details"></a>런타임 SDK 사용 및 세부 정보

이 섹션에서는 런타임 SDK를 사용 하는 방법에 대 한 기본 사항을 제공 합니다. 샘플 응용 프로그램을 탐색 하는 데 충분 한 컨텍스트를 제공 하 여 개체 앵커가 어떻게 전체적으로 사용 되는지 확인 해야 합니다.

### <a name="initialization"></a>초기화

응용 프로그램은 API를 호출 하 `ObjectObserver.IsSupported()` 여 장치에서 개체 앵커가 지원 되는지 확인 해야 합니다. API가 `ObjectObserver.IsSupported()` 를 반환 하는 경우 `false` 응용 프로그램에서 최신 HoloLens OS로 **spatialPerception** 기능 및/또는 업그레이드를 사용 하도록 설정 했는지 확인 합니다.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

그런 다음 응용 프로그램은 개체 관찰자를 만들고 [개체 앵커 수집 서비스](../quickstarts/get-started-model-ingestion.md)에 의해 생성 된 필요한 모델을 로드 합니다.

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

응용 프로그램은 공간 내에서 해당 모델의 인스턴스를 검색 하는 쿼리를 만듭니다.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

기본적으로 검색 된 각 인스턴스는 **관찰자** 에 의해 자동으로 추적 됩니다. 이러한 인스턴스는 추적 모드를 변경 하거나 상태 변경 이벤트를 수신 하 여 선택적으로 조작할 수 있습니다.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

상태 변경 이벤트에서 추적이 손실 된 경우 최신 상태를 쿼리하거나 인스턴스를 삭제할 수 있습니다.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

또한 응용 프로그램은 필요에 따라 오프 라인 디버깅을 위해 하나 또는 여러 개의 진단 세션을 기록할 수 있습니다.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

마지막으로 모든 개체를 삭제 하 여 리소스를 해제 합니다.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
