---
title: 메시
description: Azure Remote Rendering의 범위에서 메시 정의
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594521"
---
# <a name="meshes"></a>메시

## <a name="mesh-resource"></a>메시 리소스

메시는 [모델 변환](../how-tos/conversion/model-conversion.md)을 통해서만 만들 수 있는 변경 불가능한 [공유 리소스](../concepts/lifetime.md)입니다. 메시는 [raycast 쿼리](../overview/features/spatial-queries.md)를 위한 물리적 표현과 함께 하나 또는 여러 개의 *하위 메시* 가 포함됩니다. 각 하위 메시는 기본적으로 렌더링되어야 하는 [재질](materials.md)을 참조합니다. 3D 공간에 메시를 배치하려면 [Entity](entities.md)에 [MeshComponent](#meshcomponent)를 추가합니다.

### <a name="mesh-resource-properties"></a>메시 리소스 속성

`Mesh` 클래스 속성은 다음과 같습니다.

* **재질:** 재질의 배열입니다. 각 재질은 다른 하위 메시에서 사용됩니다. 배열의 여러 항목은 동일한 [재질](materials.md)을 참조할 수 있습니다. 이 데이터는 런타임 시 수정할 수 없습니다.

* **경계:** 메시 꼭짓점의 로컬 공간 AABB(축 정렬 경계 상자)입니다.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent` 클래스는 메시 리소스의 인스턴스를 배치하는 데 사용됩니다. 각 MeshComponent는 단일 메시를 참조합니다. 각 하위 메시를 렌더링하는 데 사용되는 재질을 재정의할 수 있습니다.

### <a name="meshcomponent-properties"></a>MeshComponent 속성

* **메시:** 이 구성 요소에서 사용하는 메시 리소스입니다.

* **재질:** 메시 구성 요소 자체에 지정된 재질의 배열입니다. 배열의 길이는 항상 메시 리소스의 *재질* 배열과 동일합니다. 메시 기본값에서 재정의되지 않아야 하는 재질은 이 배열에서 *null* 로 설정됩니다.

* **UsedMaterials:** 각 하위 메시에 실제로 사용되는 재질의 배열입니다. null이 아닌 값에 대한 *재질* 배열의 데이터와 동일합니다. 그렇지 않으면 메시 인스턴스의 *재질* 배열 값이 포함됩니다.

### <a name="sharing-of-meshes"></a>메시 공유

`Mesh` 리소스는 메시 구성 요소의 여러 인스턴스에서 공유할 수 있습니다. 또한 메시 구성 요소에 할당된 `Mesh` 리소스는 언제든지 프로그래밍 방식으로 변경할 수 있습니다. 아래 코드에서는 메시를 복제하는 방법을 보여줍니다.

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API 설명서

* [C# 메시 클래스](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent 클래스](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ 메시 클래스](/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent 클래스](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>다음 단계

* [재질](materials.md)