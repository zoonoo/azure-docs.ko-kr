---
title: 평면 잘라내기
description: 잘린 평면의 정의와 사용 방법에 대해 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681026"
---
# <a name="cut-planes"></a>평면 잘라내기

*잘림 평면은* 가상 평면의 한쪽에 픽셀을 자르는 시각적 기능이 며 [메시](../../concepts/meshes.md)내부를 표시 합니다.
아래 이미지는 효과를 보여 줍니다. 왼쪽에는 원본 메쉬가 표시 되 고 오른쪽에는 메시 내에서 볼 수 있습니다.

![평면 잘라내기](./media/cutplane-1.png)

## <a name="limitations"></a>제한 사항

* 이 시점에서 Azure 원격 렌더링은 **최대 8 개의 활성 잘림 평면**을 지원 합니다. 더 많은 수의 더 많은 구성 요소를 만들 수 있지만 동시에 사용 하도록 설정 하려고 하면 활성화가 무시 됩니다. 장면에 영향을 주는 구성 요소를 전환 하려면 먼저 다른 평면을 사용 하지 않도록 설정 합니다.
* 각 잘림 평면은 원격으로 렌더링 되는 모든 개체에 영향을 줍니다. 현재 특정 개체 또는 메시 파트를 제외할 수 있는 방법은 없습니다.
* 잘라내기 평면은 시각적 기능 이지만 [공간 쿼리의](spatial-queries.md)결과에는 영향을 주지 않습니다. 자르기 열린 메시로 캐스트 하려는 경우 광선의 시작점을 자르기 평면에 맞게 조정할 수 있습니다. 이러한 방식으로 광선은 표시 되는 부품만 적중 될 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

렌더링 하는 동안 각 활성 자르기 평면에는 약간의 비용이 발생 합니다. 필요 하지 않은 경우에는 잘라내기 평면을 사용 하지 않도록 설정 하거나 삭제 합니다.

## <a name="cutplanecomponent"></a>CutPlaneComponent

*CutPlaneComponent*를 만들어 장면에 잘린 평면을 추가 합니다. 평면의 위치와 방향은 구성 요소의 소유자 [엔터티에](../../concepts/entities.md)의해 결정 됩니다.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 속성

다음 속성은 잘라내기 평면 구성 요소에서 노출 됩니다.

* **사용:** 구성 요소를 사용 하지 않도록 설정 하 여 잘림 평면을 일시적으로 해제할 수 있습니다. 사용 하지 않도록 설정 된 잘라내기 평면은 렌더링 오버 헤드를 초래 하지 않으며 전역 잘림 평면 제한에도 해당 하지 않습니다.

* **보통:** 평면으로 사용 되는 방향 (+ X,-X, + Y,-Y, + Z,-Z)을 지정 합니다. 이 방향은 소유자 엔터티의 방향을 기준으로 합니다. 정확한 배치를 위해 소유자 엔터티를 이동 하 고 회전 합니다.

* **FadeColor** 및 **fadelength**

  *FadeColor* 의 알파 값이 0이 아니면 잘림 평면에 가까운 픽셀은 FADECOLOR의 RGB 부분으로 페이드 인 됩니다. 알파 채널의 강도에 따라 페이드 색 또는 부분적 으로만 페이드를 수행할지 여부를 결정 합니다. *Fadelength* 는이 페이드가 발생 하는 거리를 정의 합니다.

## <a name="next-steps"></a>다음 단계

* [단일 면 렌더링](single-sided-rendering.md)
* [공간 쿼리](spatial-queries.md)
