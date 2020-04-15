---
title: 환경 및 재질 변경
description: Unity 장면에서 하늘 맵과 개체 재질을 수정하는 방법을 보여주는 자습서입니다.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678684"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>자습서: 환경 및 재질 변경

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 장면의 환경 맵을 변경합니다.
> * 재질 매개 변수를 수정합니다.
> * 사용자 지정 질감을 로드합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [자습서: Unity에서 원격 엔터티로 작업](working-with-remote-entities.md)에 대해 잘 알고 있다고 가정합니다. 하지만, 세션에 연결하고 모델을 로드할 수 있는 Unity 프로젝트만 있으면 됩니다. 관련 내용은 [자습서: Unity 프로젝트를 처음부터 설정](project-setup.md)을 참조하세요.

> [!TIP]
> [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 *Unity* 폴더의 모든 자습서에 대해 준비된 Unity 프로젝트가 포함되어 있으며 참조로 사용할 수 있습니다.

## <a name="change-the-environment-map"></a>환경 맵 변경

Azure Remote Rendering은 [하늘 상자](../../overview/features/sky.md)('환경 맵'이라고도 함)를 사용하여 주변 광원을 시뮬레이션하도록 지원합니다. 이 기능은 샘플 모델처럼 개체에 *[Physically Based Rendering](../../overview/features/pbr-materials.md)* (물리 기반 렌더링)이 사용되는 경우 특히 유용합니다. ARR에는 다양한 하늘 질감이 내장되어 있으며, 이 자습서에도 사용됩니다.

**RemoteSky**라는 새 스크립트를 만들어서 새 GameObject에 추가합니다. 스크립트 파일을 열고 다음 코드로 바꿉니다.

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

내장된 질감이 로드되기 때문에 위에서는 `LoadTextureFromSASAsync` 변형이 사용되었습니다. [연결된 Blob 스토리지](../../how-tos/create-an-account.md#link-storage-accounts)에서 로드하는 경우에는 `LoadTextureAsync` 변형을 사용합니다. 이것이 모델에서 어떻게 작동하는지에 대한 예시는 [모델 로드 섹션](../../concepts/models.md#loading-models)에서 찾을 수 있습니다.

코드를 실행하고 하늘 맵을 계속 토글하면 모델의 광원이 크게 달라지는 것을 볼 수 있습니다. 단, 배경은 검은색이 유지되며 실제 하늘 질감을 볼 수 없습니다. 이것은 의도적입니다. 증강 현실 디바이스를 사용하면 배경을 렌더링하는 데 방해가 되기 때문입니다. 적절한 애플리케이션에서는 실제 환경과 유사한 하늘 질감을 사용해야 합니다. 그러면 개체가 더 실제처럼 보일 수 있습니다.

## <a name="modify-materials"></a>재질 수정

이전 자습서에서는 [상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)를 사용하여 선택한 개체의 색조 색을 변경했습니다. 이번에는 개체의 [재질](../../concepts/materials.md)을 수정하여 비슷한 효과를 내려고 합니다.

우선 [두 번째 자습서](working-with-remote-entities.md)처럼 개체를 선택할 스크립트가 필요합니다. **RemoteRaycaster** 스크립트가 아직 없으면 지금 만듭니다. 해당 콘텐츠를 다음 코드로 바꿉니다.

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
}
```

구성 요소를 *RemoteRendering* 게임 개체에 추가합니다. 마우스 아래에서 개체를 선택하고 선택한 개체에 *RemoteModelEntity* 구성 요소를 추가하는 작업을 담당합니다. 이 구성 요소 클래스가 실제 재질 변경 기능을 구현하는 위치입니다.

**RemoteModelEntity** 스크립트가 아직 없으면 만든 후에 콘텐츠를 다음 코드로 바꿉니다.

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

이 코드를 실행하면 마우스로 가리키는 개체가 강조 표시됩니다. 효과는 자습서 2에서 수행한 것과 비슷하지만, 구현 방식이 다릅니다. 여기에서 선택한 개체의 재질 목록을 가져온 다음, 첫 번째 항목을 수정하여 다른 알베도 색을 적용합니다.

> [!IMPORTANT]
> 이 메서드가 모델의 올바른 부분을 강조 표시할 수 있을지 여부는 모델을 작성하는 방법에 달려 있습니다. 모든 개체가 정확히 하나의 재질을 사용하면 완벽하게 작동합니다. 하지만 모델에서 부분과 재질 간에 1:1 관계가 없으면 위의 원시 코드는 제대로 작동하지 않습니다.

## <a name="use-a-different-texture"></a>다른 질감 사용

[질감](../../concepts/textures.md)은 대개 원본 모델의 일부입니다. [모델을 변환](../../quickstarts/convert-model.md)하는 동안, 모든 질감은 필요한 런타임 형식으로 변환되어 최종 모델 파일로 패키지됩니다. 런타임 시 질감을 바꾸려면 [DDS 파일 형식](https://en.wikipedia.org/wiki/DirectDraw_Surface)으로 저장하여 Azure Blob Storage에 업로드해야 합니다. Azure Blob 컨테이너를 만드는 방법은 [이 빠른 시작 가이드](../../quickstarts/convert-model.md)를 참조하세요. Blob 컨테이너가 준비되면 Azure Storage Explorer에서 열고 끌어서 놓기를 사용하여 파일을 업로드할 수 있습니다.

런타임 측면에서 Blob 스토리지의 질감 자산을 처리하는 방법은 두 가지입니다.

* SAS URI로 질감을 처리합니다. 그러려면, 업로드한 파일을 마우스 오른쪽 단추로 클릭하고, 상황에 맞는 메뉴에서 "**공유 액세스 서명 가져오기...** "를 선택합니다. 이 SAS URI를 `LoadTextureFromSASAsync` 함수 변형과 함께 사용합니다(아래 샘플 코드 참조).
* [Blob 스토리지가 계정에 연결되어 있으면](../../how-tos/create-an-account.md#link-storage-accounts), Blob 스토리지 매개 변수로 직접 질감을 처리합니다. 이 경우에는 적절한 로딩 함수는 `LoadTextureAsync`입니다.

이제 **RemoteModelEntity** 스크립트를 열고 다음 코드를 추가하고 중복 함수를 제거합니다.

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

이 코드를 실행하고 모델을 마우스로 가리킵니다. 모델에 적절한 UV 좌표가 있으면 질감이 나타납니다. 그렇지 않으면 색이 변하는 것만 보입니다.

## <a name="next-steps"></a>다음 단계

이것으로 Unity에서 Azure Remote Rendering을 사용하는 방법에 대한 소개 시리즈를 마치겠습니다. 다음 단계에서는 [세션](../../concepts/sessions.md), [엔터티](../../concepts/entities.md), [모델](../../concepts/models.md)과 같은 ARR의 기본 개념을 숙지하여 심층적인 이해를 구축할 수 있습니다. [광원](../../overview/features/lights.md), [윤곽 렌더링](../../overview/features/outlines.md), [계층 상태 재정의](../../overview/features/override-hierarchical-state.md), [재질](../../concepts/materials.md) 등 자세하게 살펴봐야 하는 다양한 기능도 있습니다.

> [!div class="nextstepaction"]
> [Unity 게임 개체 및 구성 요소](../../how-tos/unity/objects-components.md)
