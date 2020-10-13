---
title: 메시
description: Azure 원격 렌더링 범위에서 메시 정의
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 08d80a5ec2099147c12bcecd3b52d64429837285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563967"
---
# <a name="meshes"></a>메시

## <a name="mesh-resource"></a>메시 리소스

메시는 [모델 변환을](../how-tos/conversion/model-conversion.md)통해서만 만들 수 있는 변경할 수 없는 [공유 리소스](../concepts/lifetime.md)입니다. 메시는 [raycast 쿼리](../overview/features/spatial-queries.md)를 위한 물리학 표현과 함께 하나 또는 여러 개의 *submeshes* 를 포함 합니다. 각 하위 메시에 대해는 기본적으로 렌더링 되어야 하는 [자료](materials.md) 를 참조 합니다. 3D 공간에 망상을 놓으려면 [MeshComponent](#meshcomponent) 을 [엔터티에](entities.md)추가 합니다.

### <a name="mesh-resource-properties"></a>메시 리소스 속성

`Mesh`클래스 속성은 다음과 같습니다.

* **자료:** 재질의 배열입니다. 각 자료는 다른 하위 메시에 대해에서 사용 됩니다. 배열의 여러 항목은 동일한 [자료](materials.md)를 참조할 수 있습니다. 이 데이터는 런타임에 수정할 수 없습니다.

* **범위:** 메시 꼭 짓 점의 로컬 공간 축에 맞춰진 경계 상자 (AABB)입니다.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`클래스는 메시 리소스의 인스턴스를 저장 하는 데 사용 됩니다. 각 MeshComponent는 단일 메시를 참조 합니다. 각 하위 메시에 대해를 렌더링 하는 데 사용 되는 자료를 재정의할 수 있습니다.

### <a name="meshcomponent-properties"></a>MeshComponent 속성

* **메시:** 이 구성 요소에서 사용 하는 메시 리소스입니다.

* **자료:** 메시 구성 요소 자체에 지정 된 재질의 배열입니다. 배열의 길이는 항상 메시 리소스의 *재질* 배열과 동일 합니다. 메시 기본값에서 재정의 되지 않아야 하는 자료는이 배열에서 *null* 로 설정 됩니다.

* **UsedMaterials:** 각 하위 메시에 대해에 실제로 사용 되는 자료의 배열입니다. 는 null이 아닌 값에 대 한 *재질* 배열의 데이터와 동일 합니다. 그렇지 않으면 메시 인스턴스의 *재질* 배열 값을 포함 합니다.

### <a name="sharing-of-meshes"></a>메시 공유

`Mesh`리소스는 메시 구성 요소의 여러 인스턴스에서 공유할 수 있습니다. 또한 언제 `Mesh` 든 지 메시 구성 요소에 할당 된 리소스를 프로그래밍 방식으로 변경할 수 있습니다. 아래 코드에서는 메시를 복제 하는 방법을 보여 줍니다.

```cs
Entity CloneEntityWithModel(RemoteManager manager, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = manager.CreateEntity();
        MeshComponent newMeshComp = manager.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RemoteManager> manager, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *manager->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = manager->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API 설명서

* [C # 메시 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C # MeshComponent 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C + + 메시 클래스](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [C + + MeshComponent 클래스](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>다음 단계

* [재질](materials.md)
