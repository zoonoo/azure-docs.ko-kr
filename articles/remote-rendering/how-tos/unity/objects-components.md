---
title: Unity 게임 개체 및 구성 요소
description: 원격 렌더링 엔터티 및 구성 요소와 함께 작업하는 Unity 특정 방법을 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409866"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Unity 게임 개체 및 구성 요소와 상호 작용

Azure 원격 렌더링(ARR)은 방대한 수의 개체에 대해 [최적화되어 있습니다(제한](../../reference/limits.md)사항 참조). 호스트에서 크고 복잡한 계층 구조를 관리할 수 있지만 저전력 장치에서 Unity에서 모두 복제하는 것은 불가능합니다.

따라서 모델이 호스트에 로드되면 Azure Remote Rendering는 클라이언트 장치에서 모델 구조에 대한 정보를 미러(네트워크 트래픽이 발생함)하지만 Unity의 개체 및 구성 요소를 복제하지는 않습니다. 대신 필요한 Unity 게임 오브젝트와 구성 요소를 수동으로 요청하여 오버헤드를 실제로 필요한 것으로 제한할 수 있습니다. 이렇게 하면 클라이언트 측 성능을 보다 세한 제어할 수 있습니다.

따라서 Azure 원격 렌더링의 Unity 통합에는 필요에 따라 원격 렌더링 구조를 복제하는 추가 기능이 제공됩니다.

## <a name="load-a-model-in-unity"></a>Unity에서 모델 로드

모델을 로드할 때 로드된 모델의 루트 오브젝트에 대한 참조가 표시됩니다. 이 참조는 Unity 게임 오브젝트가 아니지만 확장 방법을 `Entity.GetOrCreateGameObject()`사용하여 하나로 변환할 수 있습니다. 이 함수는 형식의 `UnityCreationMode`인수를 기대합니다. 이 패스를 `CreateUnityComponents`통과하면 새로 생성된 Unity 게임 오브젝트가 호스트에 있는 모든 원격 렌더링 구성 요소에 대한 프록시 구성 요소로 추가로 채워집니다. 그러나 오버헤드를 최소화하려면 `DoNotCreateUnityComponents`을 선호하는 것이 좋습니다.

### <a name="load-model-with-task"></a>작업이 있는 로드 모델

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Unity 코루틴이 있는 로드 모델

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>대기 패턴이 있는 로드 모델

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

위의 코드 샘플은 기본 제공 모델이 로드되므로 SAS를 통한 모델 로드 경로를 사용했습니다. Blob 컨테이너(사용 `LoadModelAsync` 및 `LoadModelParams`사용)를 통해 모델을 해결하는 것은 완전히 유사하게 작동합니다.

## <a name="remoteentitysyncobject"></a>원격엔티티싱크오브젝트

Unity 게임 개체를 만들면 `RemoteEntitySyncObject` 암시적으로 게임 오브젝트에 구성 요소가 추가됩니다. 이 구성 요소는 엔터티 변환을 서버에 동기화하는 데 사용됩니다. 기본적으로 `RemoteEntitySyncObject` 사용자는 로컬 Unity `SyncToRemote()` 상태를 서버에 동기화하기 위해 명시적으로 호출해야 합니다. `SyncEveryFrame` 활성화하면 개체가 자동으로 동기화됩니다.

A가 `RemoteEntitySyncObject` 있는 개체는 원격 자식을 인스턴스화하고 Unity 편집기에서 **어린이 표시** 버튼을 통해 표시할 수 있습니다.

![원격엔티티싱크오브젝트](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>래퍼 구성요소

원격 렌더링 엔터티에 연결된 [구성](../../concepts/components.md) 요소는 `MonoBehavior`프록시 를 통해 Unity에 노출됩니다. 이러한 프록시는 Unity의 원격 구성 요소를 나타내며 모든 수정 사항을 호스트에 전달합니다.

프록시 원격 렌더링 구성 요소를 만들려면 확장 메서드를 `GetOrCreateArrComponent`사용합니다.

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>결합 된 수명

원격 [엔터티와](../../concepts/entities.md) Unity 게임 개체의 수명은 `RemoteEntitySyncObject`. 이러한 게임 `UnityEngine.Object.Destroy(...)` 개체로 호출하면 원격 엔터티도 제거됩니다.

원격 엔터티에 영향을 주지 않고 Unity 게임 개체를 `Unbind()` 삭제하려면 `RemoteEntitySyncObject`먼저 을 호출해야 합니다.

모든 프록시 구성 요소에 대해도 마찬가지입니다. 클라이언트 측 표현만 삭제하려면 먼저 프록시 `Unbind()` 구성 요소를 호출해야 합니다.

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
* [자습서: Unity에서 원격 엔터티로 작업](../../tutorials/unity/working-with-remote-entities.md)
