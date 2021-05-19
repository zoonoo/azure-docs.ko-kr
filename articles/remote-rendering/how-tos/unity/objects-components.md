---
title: Unity 게임 개체 및 구성 요소
description: Remote Rendering 엔터티 및 구성 요소를 사용하여 작업하는 Unity 관련 메서드를 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594149"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Unity 게임 개체 및 구성 요소와 상호 작용

ARR(Azure Remote Rendering)은 많은 수의 개체에 최적화되어 있습니다([제한](../../reference/limits.md) 참조). 호스트에서 크고 복잡한 계층을 관리할 수 있지만, 저전력 디바이스에서 Unity의 모든 항목을 복제하는 것은 불가능합니다.

따라서 모델이 호스트에 로드되면 Azure Remote Rendering은 네트워크 트래픽이 발생하는 클라이언트 디바이스의 모델 구조에 대한 정보를 미러링하지만 Unity의 개체와 구성 요소는 복제하지 않습니다. 대신 필요한 Unity 게임 개체 및 구성 요소를 수동으로 요청해야 하므로 실제로 필요한 항목에 대한 오버헤드를 제한할 수 있습니다. 이렇게 하면 클라이언트 쪽 성능을 보다 효과적으로 제어할 수 있습니다.

따라서 Azure Remote Rendering의 Unity 통합에는 주문형 Remote Rendering 구조를 복제할 수 있는 추가 기능이 제공됩니다.

## <a name="load-a-model-in-unity"></a>Unity에서 모델 로드

모델을 로드하는 경우 로드된 모델의 루트 개체에 대한 참조를 가져옵니다. 이 참조는 Unity 게임 개체가 아니지만 확장 메서드 `Entity.GetOrCreateGameObject()`를 사용하여 참조로 전환할 수 있습니다. 이 함수에는 `UnityCreationMode` 형식의 인수가 필요합니다. `CreateUnityComponents`를 전달하는 경우 새로 만든 Unity 게임 개체는 호스트에 있는 모든 Remote Rendering 구성 요소에 대한 프록시 구성 요소로 추가로 채워집니다. 하지만 오버 헤드를 최소로 유지하려면 `DoNotCreateUnityComponents`를 전달하는 것이 좋습니다.

### <a name="load-model-with-unity-coroutines"></a>Unity 코루틴을 사용하여 모델 로드

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>await 패턴을 사용하여 모델 로드

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

위의 코드 샘플에서는 기본 제공 모델이 로드되었으므로 SAS를 통한 모델 로드 경로를 사용했습니다. Blob 컨테이너를 통한 모델 주소 지정(`LoadModelAsync` 및 `LoadModelOptions` 사용)은 완전히 유사하게 작동합니다.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Unity 게임 개체를 만들면 `RemoteEntitySyncObject` 구성 요소가 게임 개체에 암시적으로 추가됩니다. 이 구성 요소는 엔터티 변환을 서버에 동기화하는 데 사용됩니다. 기본적으로 `RemoteEntitySyncObject`는 사용자가 `SyncToRemote()`를 명시적으로 호출하여 로컬 Unity 상태를 서버와 동기화해야 합니다. `SyncEveryFrame`을 사용하도록 설정하면 개체가 자동으로 동기화됩니다.

`RemoteEntitySyncObject`를 사용하는 개체는 해당 원격 자식을 인스턴스화하고 **:::no-loc text="Show children":::** 단추를 통해 Unity 편집기에 표시할 수 있습니다.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>래퍼 구성 요소

Remote Rendering 엔터티에 연결된 [구성 요소](../../concepts/components.md)는 프록시 `MonoBehavior`를 통해 Unity에 노출됩니다. 이러한 프록시는 Unity의 원격 구성 요소를 나타내며 호스트에 대한 모든 수정 사항을 전달합니다.

프록시 Remote Rendering 구성 요소를 만들려면 확장 메서드 `GetOrCreateArrComponent`를 사용합니다.

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>결합된 수명

원격 [엔터티](../../concepts/entities.md)와 Unity 게임 개체의 수명은 `RemoteEntitySyncObject`를 통해 바인딩되는 동안 결합됩니다. 이러한 게임 개체를 사용하여 `UnityEngine.Object.Destroy(...)`를 호출하면 원격 엔터티도 제거됩니다.

원격 엔터티에 영향을 주지 않고 Unity 게임 개체를 삭제하려면 먼저 `RemoteEntitySyncObject`에서 `Unbind()`를 호출해야 합니다.

모든 프록시 구성 요소에 대해서도 마찬가지입니다. 클라이언트 쪽 표시만 제거하려면 먼저 프록시 구성 요소에서 `Unbind()`를 호출해야 합니다.

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>다음 단계

* [Unity용 Remote Rendering 설정](unity-setup.md)
* [자습서: Unity에서 원격 엔터티 조작](../../tutorials/unity/manipulate-models/manipulate-models.md)
