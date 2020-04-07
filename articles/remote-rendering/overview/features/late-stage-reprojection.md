---
title: 늦은 단계 재투영
description: 늦은 단계 재투영에 대한 정보와 그것을 사용하는 방법.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680987"
---
# <a name="late-stage-reprojection"></a>늦은 단계 재투영

*후기 단계 재투영(LSR)은* 사용자가 움직일 때 홀로그램을 안정화하는 데 도움이 되는 하드웨어 기능입니다.

정적 모델은 이동할 때 위치를 시각적으로 유지할 것으로 예상됩니다. 불안정해 보이는 경우 이 동작은 LSR 문제를 암시할 수 있습니다. 애니메이션이나 폭발 뷰와 같은 추가 동적 변환이 이 동작을 가릴 수 있습니다.

두 가지 다른 LSR 모드, 즉 **평면 LSR** 또는 **깊이 LSR**중에서 선택할 수 있습니다. 활성 응용 프로그램이 깊이 버퍼를 제출하는지 여부에 따라 활성 상태가 결정됩니다.

두 LSR 모드 모두 홀로그램 안정성을 향상, 그들은 그들의 뚜렷한 한계를 가지고 있지만. 대부분의 경우 더 나은 결과를 제공하기 때문에 Depth LSR을 시도하는 것으로 시작합니다.

## <a name="choose-lsr-mode-in-unity"></a>유니티에서 LSR 모드 선택

Unity 편집기에서 빌드 설정 > *파일로*이동합니다. 왼쪽 하단의 *플레이어 설정을* 선택한 다음 플레이어 *> XR 설정에서 가상 현실 SDK > > Windows 혼합 현실에서* 깊이 버퍼 공유 를 **사용하도록 선택했는지** 여부를 확인합니다.

![깊이 버퍼 공유 사용 가능 플래그](./media/unity-depth-buffer-sharing-enabled.png)

이 경우 앱에서 Depth LSR을 사용하고 그렇지 않으면 평면 LSR을 사용합니다.

## <a name="depth-lsr"></a>깊이 LSR

Depth LSR이 작동하려면 클라이언트 응용 프로그램이 LSR 중에 고려해야 할 모든 관련 형상을 포함하는 유효한 깊이 버퍼를 제공해야 합니다.

깊이 LSR은 제공된 깊이 버퍼의 내용에 따라 비디오 프레임을 안정화하려고 시도합니다. 따라서 투명 오브젝트와 같이 렌더링되지 않은 콘텐츠는 LSR에서 조정할 수 없으며 불안정및 재투영 아티팩트가 표시될 수 있습니다.

## <a name="planar-lsr"></a>평면 LSR

평면 LSR은 깊이 LSR과 마찬가지로 픽셀당 깊이 정보를 갖지 않습니다. 대신 각 프레임을 제공해야 하는 평면을 기반으로 모든 콘텐츠를 다시 투영합니다.

평면 LSR은 제공된 평면에 가장 가까운 오브젝트를 가장 잘 재투영합니다. 개체가 멀리 떨어져 있을수록 더 불안정해 보입니다. Depth LSR은 서로 다른 깊이에서 오브젝트를 다시 투영하는 것이 더 좋지만 평면 LSR은 평면과 잘 정렬되는 콘텐츠에 더 잘 작동할 수 있습니다.

### <a name="configure-planar-lsr-in-unity"></a>유니티에서 평면 LSR 구성

평면 매개변수는 을 통해 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`모든 프레임을 제공해야 하는 소위 포커스 *포인트에서*파생됩니다. 자세한 내용은 [유니티 포커스 포인트 API를](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 참조하십시오. 포커스 포인트를 설정하지 않으면 대체가 선택됩니다. 그러나 자동 대체는 종종 최적이 아닌 결과로 이어집니다.

원격 렌더링 호스트에서 계산한 포커스 포인트를 기준으로 하는 것이 합리적일 수 있지만 포커스 포인트를 직접 계산할 수 있습니다. 그것을 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` 얻기 위해 전화. 포커스 포인트를 표현할 좌표 프레임을 제공하라는 메시지가 표시됩니다. 대부분의 경우 `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` 여기에서 결과를 제공하려고 합니다.

일반적으로 클라이언트와 호스트는 클라이언트의 UI 요소와 같이 상대방이 인식하지 못하는 콘텐츠를 렌더링합니다. 따라서 원격 포커스 포인트를 로컬로 계산된 포커스 포인트와 결합하는 것이 합리적일 수 있습니다.

두 개의 연속 프레임에서 계산된 초점 포인트는 매우 다를 수 있습니다. 단순히 있는 대로 사용하면 홀로그램이 점프하는 것처럼 나타날 수 있습니다. 이 동작을 방지하려면 이전 포커스 포인트와 현재 포커스 포인트 간에 보간하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [서버 측 성능 쿼리](performance-queries.md)
