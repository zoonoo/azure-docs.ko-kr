---
title: 구성 요소
description: Azure 원격 렌더링 범위에서 구성 요소 정의
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681897"
---
# <a name="components"></a>구성 요소

Azure 원격 렌더링은 [엔터티 구성 요소 시스템](https://en.wikipedia.org/wiki/Entity_component_system) 패턴을 사용 합니다. [엔터티](entities.md) 는 개체의 위치 및 계층 구조 컴퍼지션을 나타내므로 구성 요소는 동작을 구현 합니다.

가장 자주 사용 되는 구성 요소 유형은 메시를 렌더링 파이프라인에 추가 하는 [메시 구성 요소](meshes.md)입니다. 마찬가지로 조명 [구성 요소](../overview/features/lights.md) 는 조명을 추가 하는 데 사용 되 고, [잘라내기 평면 구성 요소](../overview/features/cut-planes.md) 는 열린 메시를 잘라내는 데 사용 됩니다.

이러한 모든 구성 요소는 참조 지점으로 연결 된 엔터티의 변환 (위치, 회전, 배율)을 사용 합니다.

## <a name="working-with-components"></a>구성 요소 작업

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

구성 요소는 생성 시 엔터티에 연결 됩니다. 나중에 다른 엔터티로 이동할 수 없습니다. 구성 요소는 구성 요소의 `Component.Destroy()` 소유자 엔터티가 제거 될 때를 사용 하 여 명시적으로 또는 자동으로 삭제 됩니다.

한 번에 각 구성 요소 형식의 인스턴스 하나만 엔터티에 추가할 수 있습니다.

## <a name="unity-specific"></a>Unity 관련

Unity 통합에는 구성 요소와 상호 작용 하기 위한 추가 확장 기능이 있습니다. [Unity 게임 개체 및 구성 요소를](../how-tos/unity/objects-components.md)참조 하세요.

## <a name="next-steps"></a>다음 단계

* [개체 경계](object-bounds.md)
* [메시](meshes.md)
