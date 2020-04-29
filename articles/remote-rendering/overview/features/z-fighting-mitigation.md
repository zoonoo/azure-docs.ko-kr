---
title: Z-fighting 완화
description: Z-싸의 아티팩트를 완화 하는 기술에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680454"
---
# <a name="z-fighting-mitigation"></a>Z-fighting 완화

두 서피스가 겹치면 다른 서피스가 서로 위에 렌더링 되어야 하는지 명확 하지 않습니다. 결과는 픽셀 마다 다르므로 뷰 종속 아티팩트가 생성 됩니다. 따라서 카메라 또는 메쉬가 이동 하면 이러한 패턴이 현저 하 게 깜박입니다. 이 아티팩트를 *z-싸*라 합니다. AR 및 VR 응용 프로그램의 경우 헤드 탑재 된 장치가 자연스럽 게 항상 이동 하기 때문에 문제가 intensified. 뷰어 discomfort z-중단 완화 기능을 Azure 원격 렌더링에서 사용할 수 없도록 방지 합니다.

## <a name="z-fighting-mitigation-modes"></a>Z-완화 완화 모드

|적합                        | 결과                               |
|---------------------------------|:-------------------------------------|
|일반 z-싸 워               |![Z-싸 우](./media/zfighting-0.png)|
|Z-싸 완화 기능 사용    |![Z-싸 우](./media/zfighting-1.png)|
|바둑판 강조 표시 사용|![Z-싸 우](./media/zfighting-2.png)|

다음 코드는 z-싸 우기 기능을 활성화 합니다.

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
> Z-완화 완화는 렌더링 된 모든 메시에 영향을 주는 전역 설정입니다.

## <a name="reasons-for-z-fighting"></a>Z-싸의 이유

Z-는 다음과 같은 두 가지 이유로 주로 발생 합니다.

1. 카메라에서 서피스가 멀리 떨어져 있는 경우 깊이 값의 정밀도가 떨어지고 값을 구별할 수 없게 됩니다.
1. 메시의 표면이 물리적으로 겹치는 경우

첫 번째 문제는 항상 발생할 수 있으며 제거 하기 어렵습니다. 응용 프로그램에서이 문제가 발생 하는 경우 *근거리 평면* 거리와 *far* 거리의 비율이 실질적으로 낮은 지 확인 합니다. 예를 들어 거리가 0.01 인 1000 far 비행기의 근거리 비행기는에 가까운 0.1 평면을 포함 하 고 거리가 20 인 far 비행기를 포함 하는 것 보다 훨씬 이전에이 문제를 만듭니다.

두 번째 문제는 잘못 작성 된 콘텐츠를 나타내는 지표입니다. 실제 환경에서는 두 개의 개체가 동시에 동일한 위치에 있을 수 없습니다. 응용 프로그램에 따라 사용자는 겹치는 서피스가 존재 하는지 여부를 알 수 있습니다. 예를 들어 실제 세계 생성의 기초가 되는 건물의 CAD 장면에는 실제로 불가능 한 표면 교차점을 포함 하지 않아야 합니다. 시각적 검사를 허용 하기 위해 강조 표시 모드를 사용할 수 있으며,이를 통해 애니메이션 된 바둑판 패턴으로 잠재적 z-싸 볼 수 있습니다.

## <a name="limitations"></a>제한 사항

제공 된 z-싸 인 한 완화 방법이 가장 좋습니다. 모든 z를 제거 하는 것은 보장 되지 않습니다. 또한 한 화면을 다른 화면으로 자동으로 사용 합니다. 따라서 서로 너무 가까운 서피스가 있으면 "잘못 된" 화면이 맨 위에 표시 될 수 있습니다. 일반적인 문제는 텍스트 및 기타 decals 화면에 적용 되는 경우입니다. Z-싸 낮은 완화 기능을 사용 하도록 설정 하면 이러한 세부 정보는 쉽게 사라질 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

* Z-싸 인 한 완화를 사용 하면 성능 오버 헤드가 거의 발생 하지 않습니다.
* 또한 z-싸 오버레이를 사용 하도록 설정 하면 사소한 성능 오버 헤드가 발생 하지만 장면에 따라 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)
