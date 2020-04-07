---
title: 절단 평면
description: 절단 평면이 무엇이며 어떻게 사용하는지 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681026"
---
# <a name="cut-planes"></a>절단 평면

*절단 평면은* 가상 평면의 한쪽에 픽셀을 클립하여 메시 내부를 드러내는 시각적 [기능입니다.](../../concepts/meshes.md)
아래 이미지는 효과를 보여 줍니다. 왼쪽은 원래 메시를 표시하고, 오른쪽메시는 메시 내부를 볼 수 있습니다.

![절단 평면](./media/cutplane-1.png)

## <a name="limitations"></a>제한 사항

* 당분간 Azure 원격 렌더링은 **최대 8개의 활성 절단 평면을**지원합니다. 더 많은 절단 평면 구성요소를 작성할 수 있지만 더 동시에 활성화하려고 하면 활성화가 무시됩니다. 장면에 영향을 줄 구성 요소를 전환하려면 먼저 다른 평면을 사용하지 않도록 설정합니다.
* 각 절단 평면은 원격으로 렌더링된 모든 오브젝트에 영향을 줍니다. 현재 특정 오브젝트 또는 메시 부품을 제외할 수 있는 방법은 없습니다.
* 절단 평면은 순전히 시각적 인 기능이며 [공간 쿼리의](spatial-queries.md)결과에 영향을 미치지 않습니다. 캐스트를 컷 오픈 메시로 광선 으로 지정하려면 광선의 시작점을 절단 평면에 맞게 조정할 수 있습니다. 이렇게 하면 광선이 보이는 부분만 칠 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

각 활성 절단 평면은 렌더링 중에 약간의 비용이 발생합니다. 필요하지 않을 때 절단 평면을 비활성화하거나 삭제합니다.

## <a name="cutplanecomponent"></a>절단평면 구성요소

컷 평면 구성 요소를 작성하여 장면에 *절단 평면을*추가합니다. 평면의 위치와 방향은 구성요소의 소유자 [엔터티에](../../concepts/entities.md)의해 결정됩니다.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>절단평면 구성요소 속성

절단 평면 구성요소에 는 다음 속성이 노출됩니다.

* **사용 설정:** 구성요소를 사용하지 않도록 설정하여 절단 평면을 일시적으로 끌 수 있습니다. 비활성화된 절단 평면은 렌더링 오버헤드를 발생하지 않으며 전역 컷 평면 제한에도 포함되지 않습니다.

* **보통:** 평면 법선으로 사용되는 방향(+X,-X, +Y, Y,+Z,-Z)을 지정합니다. 이 방향은 소유자 엔터티의 방향을 기준으로 합니다. 소유자 엔터티를 이동하고 회전하여 정확한 배치를 합니다.

* **페이드 컬러** 및 **페이드 길이:**

  *페이드 컬러의* 알파 값이 0이 아닌 경우 절단 평면에 가까운 픽셀은 페이드 컬러의 RGB 부분쪽으로 페이드됩니다. 알파 채널의 강도는 페이드 색상쪽으로 완전히 페이드할지 아니면 부분적으로만 페이드할지 여부를 결정합니다. *페이드길이는* 이 페이드가 수행될 거리를 정의합니다.

## <a name="next-steps"></a>다음 단계

* [단일 양면 렌더링](single-sided-rendering.md)
* [공간 쿼리](spatial-queries.md)
