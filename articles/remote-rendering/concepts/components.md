---
title: 구성 요소
description: Azure 원격 렌더링 범위의 구성 요소 정의
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681897"
---
# <a name="components"></a>구성 요소

Azure 원격 [렌더링은 엔터티 구성 요소 시스템](https://en.wikipedia.org/wiki/Entity_component_system) 패턴을 사용합니다. [엔터티는](entities.md) 개체의 위치와 계층적 구성을 나타내지만 구성 요소는 동작을 구현합니다.

가장 자주 사용되는 구성 요소 유형은 렌더링 파이프라인에 메시를 추가하는 [메시 구성 요소입니다.](meshes.md) 마찬가지로 [라이트 구성요소는](../overview/features/lights.md) 조명을 추가하고 [평면 부품을 절단하여](../overview/features/cut-planes.md) 열린 매쉬를 절단하는 데 사용됩니다.

이러한 모든 구성요소는 연결된 엔티티의 변환(위치, 회전, 배율)을 참조점으로 사용합니다.

## <a name="working-with-components"></a>부품 작업

프로그래밍 방식으로 구성 요소를 쉽게 추가, 제거 및 조작할 수 있습니다.

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

구성 요소는 작성 시 엔터티에 연결됩니다. 나중에 다른 엔터티로 이동할 수 없습니다. 구성 요소의 `Component.Destroy()` 소유자 엔터티가 소멸되면 구성 요소가 명시적으로 삭제되거나 자동으로 삭제됩니다.

각 구성 요소 유형의 인스턴스는 한 번에 엔터티에 하나만 추가할 수 있습니다.

## <a name="unity-specific"></a>유니티 특정

Unity 통합에는 구성 요소와 상호 작용하기 위한 추가 확장 기능이 있습니다. [Unity 게임 개체 및 구성 요소를](../how-tos/unity/objects-components.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [개체 경계](object-bounds.md)
* [메시](meshes.md)
