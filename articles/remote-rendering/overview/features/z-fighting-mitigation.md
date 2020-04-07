---
title: Z-파이팅 완화
description: z-fighting 아티팩트를 완화하는 기술 설명
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680454"
---
# <a name="z-fighting-mitigation"></a>Z-파이팅 완화

두 서피스가 겹치면 어떤 서피스를 다른 서피스 위에 렌더링해야 하는지 명확하지 않습니다. 그 결과 픽셀마다 달라지므로 뷰 종속 아티팩트가 생성됩니다. 따라서 카메라 나 메시가 움직일 때 이러한 패턴이 눈에 띄게 깜박입니다. 이 아티팩트를 *z-fighting라고*합니다. AR 및 VR 애플리케이션의 경우 헤드 마운트 장치가 항상 자연스럽게 움직이기 때문에 문제가 심화됩니다. 뷰어의 불편함을 방지하기 위해 Z-fighting 완화 기능은 Azure 원격 렌더링에서 사용할 수 있습니다.

## <a name="z-fighting-mitigation-modes"></a>Z-파이팅 완화 모드

|상황                        | 결과                               |
|---------------------------------|:-------------------------------------|
|일반 Z-파이팅               |![Z-파이팅](./media/zfighting-0.png)|
|Z-파이팅 완화 사용    |![Z-파이팅](./media/zfighting-1.png)|
|바둑판 강조 표시 가 사용 가능|![Z-파이팅](./media/zfighting-2.png)|

다음 코드를 통해 z-fighting 완화를 수행할 수 있습니다.

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z-fighting 완화는 렌더링된 모든 메시에 영향을 주는 전역 설정입니다.

## <a name="reasons-for-z-fighting"></a>z-파이팅의 이유

Z-파이팅은 주로 두 가지 이유로 발생합니다.

1. 표면이 카메라에서 매우 멀리 떨어져 있으면 깊이 값의 정밀도가 저하되고 값이 구별되지 않습니다.
1. 메시의 표면이 물리적으로 겹치는 경우

첫 번째 문제는 항상 발생할 수 있으며 제거하기가 어렵습니다. 응용 프로그램에서 이러한 경우 *가까운 평면* 거리와 *원거리* 거리의 비율이 실용적이지 않은지 확인합니다. 예를 들어 거리 0.01의 가까운 평면과 거리 1000의 원거리 평면은 0.1에서 가까운 평면과 거리 20의 원거리 평면을 갖는 것보다 훨씬 일찍 이 문제를 만듭니다.

두 번째 문제는 잘못 작성된 콘텐츠에 대한 지표입니다. 실제 환경에서는 두 개체가 동시에 같은 위치에 있을 수 없습니다. 응용 프로그램에 따라 사용자는 겹치는 서피스가 있는지 여부와 서피스가 있는 위치를 알고 싶을 수 있습니다. 예를 들어 실제 구조의 기초가 되는 건물의 CAD 장면에는 물리적으로 불가능한 표면 교차점이 포함되어서는 안 됩니다. 육안 검사를 위해 하이라이트 모드를 사용할 수 있으며, 이 모드는 잠재적인 z-fighting를 애니메이션 바둑판 패턴으로 표시합니다.

## <a name="limitations"></a>제한 사항

제공된 z-fighting 완화는 최선의 노력입니다. 모든 z-전투를 제거한다는 보장은 없습니다. 또한 자동으로 다른 표면보다 하나의 표면을 선호합니다. 따라서 서로 너무 가까운 서피스가 있는 경우 "잘못된" 서피스가 맨 위에 있는 것처럼 보일 수 있습니다. 일반적인 문제는 텍스트 및 기타 데칼을 서피스에 적용하는 경우입니다. z-fighting 완화를 통해 이러한 세부 사항은 쉽게 사라질 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

* z-fighting 완화를 사용하도록 설정하면 성능 오버헤드가 거의 또는 전혀 발생하지 않습니다.
* 또한 z-fighting 오버레이를 사용하도록 설정하면 장면에 따라 다를 수 있지만 사소한 성능 오버헤드가 발생합니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [늦은 단계 재투영](late-stage-reprojection.md)
