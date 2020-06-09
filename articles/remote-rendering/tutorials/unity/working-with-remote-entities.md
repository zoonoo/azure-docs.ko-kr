---
title: Unity에서 원격 엔터티로 작업
description: ARR 엔터티로 작업하는 방법을 보여주는 자습서입니다.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021248"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>자습서: Unity에서 원격 엔터티로 작업

[자습서: Unity 프로젝트를 처음부터 설정](project-setup.md)에서는 Azure Remote Rendering에서 작업할 새 Unity 프로젝트를 구성하는 방법을 보여줍니다. 이 자습서에서는 모든 ARR 사용자에게 필요한 가장 일반적인 기능을 살펴봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 광선 투사(ray cast)를 사용하여 개체를 선택합니다.
> * 색조 색, 선택 상태, 표시 여부 등의 개체 상태를 재정의합니다.
> * 원격 엔터티를 삭제합니다.
> * 원격 엔터티를 옮깁니다.
> * 절단면을 사용하여 개체 내부를 봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: Unity 프로젝트를 처음부터 설정](project-setup.md)을 기반으로 합니다.

> [!TIP]
> [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 *Unity* 폴더의 모든 자습서에 대해 준비된 Unity 프로젝트가 포함되어 있으며 참조로 사용할 수 있습니다.

## <a name="pick-objects"></a>개체 선택

개체와 상호 작용이 필요합니다. 따라서 먼저 필요한 작업은 마우스 커서 아래에 개체를 선택하는 것입니다.

**RemoteRaycaster**라는 [새 스크립트](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)를 만들어서 전체 콘텐츠를 아래 코드로 바꿉니다.

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

이 구성 요소를 장면의 *RemoteRendering* 개체에 추가합니다.

> [!WARNING]
>
> *RemoteRaycaster* 구성 요소를 사용하려면 동일한 개체에 *ARRServiceUnity* 구성 요소가 연결되어야 합니다. *ARRServiceUnity*는 일부 ARR 기능에 보다 쉽게 액세스할 수 있는 도우미 클래스입니다. 단, 장면에는 이 구성 요소의 인스턴스가 하나만 있을 수 있습니다. 따라서 *ARRServiceUnity*가 필요한 모든 구성 요소를 동일한 GameObject에 추가해야 합니다.
> ARR 기능을 여러 게임 개체에서 액세스하려면 그 중 하나에만 *ARRServiceUnity* 구성 요소를 추가하고 다른 스크립트에서 참조하거나 ARR 기능에 직접 액세스합니다.

재생을 누르고 세션에 연결하여 모델을 로드합니다. 이제 장면의 개체를 가리키고 콘솔 출력을 봅니다. 마우스로 가리킨 각 부분의 개체 이름이 출력됩니다.

## <a name="highlight-objects"></a>개체 강조 표시

다음 단계에서는 사용자가 모델의 어느 부분을 가리키는지에 대한 시각적 피드백을 주려고 합니다. 이 기능을 구현하기 위해 선택된 엔터티에 [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md)를 연결합니다. 이 구성 요소는 개체에 다양한 기능을 사용하거나 사용하지 않도록 설정하는 데 사용됩니다. 여기에서는 색조 색을 설정하고 [윤곽 렌더링](../../overview/features/outlines.md)을 사용하도록 설정하는 데 사용합니다.

**RemoteModelEntity**라는 스크립트 파일을 하나 더 만들어서 콘텐츠를 다음 코드로 바꿉니다.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> 이 스크립트는 아래 코드에 의해 프로그래밍 방식으로 할당되므로 Unity 게임 개체에 할당하지 마세요.

다음으로, *RemoteRaycaster*를 확장하여 방금 선택한 개체에 *RemoteModelEntity*를 추가해야 합니다.

**RemoteRaycaster** 구현에 다음 코드를 추가하고 중복 함수를 제거합니다.

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

프로젝트를 실행하고 모델을 가리키면 붉은 색조와 흰색 선택 윤곽선이 보입니다.

## <a name="isolate-the-selected-object"></a>선택한 개체 격리

[HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md)의 또 다른 용도는 표시 여부를 재정의하는 기능이며, 선택한 개체를 모델의 나머지 부분과 격리할 수 있습니다. **RemoteModelEntity** 스크립트를 열고 다음 코드를 추가하고 중복 함수를 제거합니다.

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

이 코드는 계층의 최상위 개체에 상태 재정의 구성 요소가 있어야 모든 개체가 보이지 않게 됩니다. 그런 다음, 선택한 개체에서 표시 여부를 다시 재정의하여 개체가 보이도록 합니다. 따라서 루트 개체에 상태 재정의 구성 요소를 만들어야 합니다.

**RemoteRendering** 스크립트를 열고 *LoadModel* 함수 맨 위에 아래 코드를 삽입합니다.

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

마지막으로 표시 여부를 토글할 수 있는 방법이 필요 합니다. **RemoteRaycaster** 스크립트를 열고 *Update* 함수를 바꿉니다.

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

코드를 실행하고 모델의 특정 부분을 마우스 오른쪽 단추로 클릭합니다. 모델의 나머지 부분은 사라지고 강조 표시된 부분만 계속 보입니다.

## <a name="remove-gameobject-instances-of-remote-entities"></a>원격 엔터티의 GameObject 인스턴스 제거

코드는 개체를 계속 생성하지만 정리는 하지 않는 것을 알 수 있습니다. 이런 점은 개체 계층 구조 패널에서도 볼 수 있습니다. 시뮬레이션하는 동안 원격 개체 계층 구조를 확장하면, 모델의 새 부분을 마우스로 가리킬 때마다 점점 더 많은 개체가 표시되는 것을 볼 수 있습니다.

장면에 개체가 많으면 성능에 부정적인 영향을 줍니다. 더 이상 필요하지 않은 개체는 항상 정리해야 합니다.

아래 코드를 **RemoteRaycaster** 스크립트에 삽입하고 중복 함수를 제거합니다.

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>개체 이동

다음 단계에서는 선택한 개체를 옮기려고 합니다. **RemoteRaycaster** 스크립트에 아래 코드를 삽입하고 중복 함수를 제거합니다.

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> 이 코드를 실행하면 아무 작업도 수행되지 않습니다. 성능상의 이유로 개체의 변형을 변경해도 상태 변화가 서버에 자동으로 동기화되지 않기 때문입니다. 대신 상태 변화를 서버에 수동으로 푸시하거나 *RemoteEntitySyncObject* 구성 요소에서 **SyncEveryFrame**을 사용하도록 설정해야 합니다.

**RemoteModelEntity** 스크립트를 열고 다음 줄을 추가합니다.

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

코드를 다시 실행하면 개체를 마우스 왼쪽 단추로 클릭하고 끌어서 옮길 수 있습니다.

## <a name="add-a-cut-plane"></a>절단면 추가

이 자습서에서 시도해 볼 마지막 기능은 [절단면](../../overview/features/cut-planes.md) 사용입니다. 절단면은 렌더링된 개체의 일부를 잘라내어 내부를 볼 수 있도록 합니다.

**CutPlane** 장면에 새 GameObject를 만듭니다. 새 스크립트를 만들어서 **RemoteCutPlane**이라고 부릅니다. 새 GameObject에 구성 요소를 추가합니다.

스크립트 파일을 열고 콘텐츠를 다음 코드로 바꿉니다.

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

이제 코드를 실행하면 절단면이 모델을 어떻게 절개하는지 볼 수 있습니다. *CutPlane* 개체를 선택하고 *장면* 창에서 옮기고 회전할 수 있습니다. 절단면 개체를 사용하지 않도록 설정하여 절단면 토글을 켜거나 끌 수 있습니다.

## <a name="next-steps"></a>다음 단계

원격 개체와 상호 작용하는 데 가장 중요한 기능을 알아보았습니다. 다음 자습서에서는 장면의 모양을 사용자 지정하는 방법을 살펴보겠습니다.

> [!div class="nextstepaction"]
> [자습서: 환경 및 재질 변경](changing-environment-and-materials.md)
