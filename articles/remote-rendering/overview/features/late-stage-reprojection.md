---
title: 후기 단계 다시 프로젝션
description: 늦은 단계 다시 프로젝션 및 사용 방법에 대 한 정보입니다.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680987"
---
# <a name="late-stage-reprojection"></a>후기 단계 다시 프로젝션

Lsr ( *후반 단계 Reprojection* )은 사용자가 이동할 때 안정화 holograms 하는 데 도움이 되는 하드웨어 기능입니다.

정적 모델은 이동 하는 경우 해당 위치를 시각적으로 유지 해야 합니다. 불안정 한 것으로 표시 되는 경우이 동작은 LSR 문제에 대 한 힌트를 표시할 수 있습니다. 애니메이션 또는 폭발 뷰와 같은 추가 동적 변환은이 동작을 마스킹할 수 있습니다.

두 개의 다른 LSR 모드 (예, **평면 lsr** 또는 **Depth lsr**) 중에서 선택할 수 있습니다. 활성 상태는 클라이언트 응용 프로그램이 깊이 버퍼를 전송 하는지 여부에 따라 결정 됩니다.

두 LSR 모드는 각각 별도의 제한이 있지만 홀로그램의 안정성을 향상 시킵니다. 대부분의 경우 더 나은 결과를 제공 하므로 깊이 LSR을 사용해 시작 합니다.

## <a name="choose-lsr-mode-in-unity"></a>Unity에서 LSR 모드 선택

Unity 편집기에서 *파일 > 빌드 설정*으로 이동 합니다. 왼쪽 아래에서 *플레이어 설정* 을 선택한 다음 *플레이어 > XR 설정 > 가상 현실 Sdk > Windows Mixed Reality* 에서 **깊이 버퍼 공유 사용** 이 선택 되어 있는지 확인 합니다.

![깊이 버퍼 공유 사용 플래그](./media/unity-depth-buffer-sharing-enabled.png)

이 경우 앱은 깊이 있는 LSR를 사용 하 고, 그렇지 않은 경우에는 평면 LSR를 사용 합니다.

## <a name="depth-lsr"></a>깊이 LSR

Depth LSR가 작동 하려면 클라이언트 응용 프로그램에서 LSR 중에 고려해 야 하는 모든 관련 기 하 도형을 포함 하는 유효한 깊이 버퍼를 제공 해야 합니다.

깊이 LSR에서는 제공 된 깊이 버퍼의 내용에 따라 비디오 프레임을 안정화 하려고 시도 합니다. 결과적으로 투명 개체와 같이 렌더링 되지 않은 콘텐츠는 LSR에서 조정할 수 없으며 불안정성 및 reprojection 아티팩트가 표시 될 수 있습니다.

## <a name="planar-lsr"></a>평면 LSR

평면 LSR에는 깊이 있는 자세한 정보가 포함 되어 있지 않습니다. 대신 각 프레임을 제공 해야 하는 평면을 기반으로 모든 콘텐츠를 다시 프로젝션 합니다.

평면 LSR은 제공 된 평면과 가까이 있는 개체를 가장 잘 프로젝션 합니다. 개체를 추가 하는 것은 더 불안정 합니다. 깊이 LSR는 서로 다른 수준에서 개체를 다시 프로젝션 하는 것이 더 낫지만, 평면에는 콘텐츠 정렬에 대해 평면 LSR이 더 적합할 수 있습니다.

### <a name="configure-planar-lsr-in-unity"></a>Unity에서 평면 LSR 구성

평면 매개 변수는 모든 프레임을 통해 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`제공 해야 하는 *포커스 지점*이라고 하는에서 파생 됩니다. 자세한 내용은 [Unity Focus POINT API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 를 참조 하세요. 포커스 지점을 설정 하지 않으면 대체 (fallback)가 선택 됩니다. 그러나 자동 대체는 자주 발생 하지 않는 결과를 초래 합니다.

원격 렌더링 호스트에서 계산 된 항목을 기반으로 하는 것이 적합할 수 있지만 포커스 지점을 직접 계산할 수 있습니다. 을 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` (를) 호출 하 여를 가져옵니다. 포커스 지점을 표현할 좌표 프레임을 제공 하 라는 메시지가 표시 됩니다. 대부분의 경우 여기에서 `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` 결과를 제공 하는 것이 좋습니다.

일반적으로 클라이언트와 호스트는 모두 클라이언트의 UI 요소와 같이 다른 쪽에서 인식 되지 않는 콘텐츠를 렌더링 합니다. 따라서 원격 포커스 지점을 로컬로 계산 된 것과 결합 하는 것이 적합할 수 있습니다.

연속 된 두 프레임에서 계산 되는 포커스 점은 매우 다를 수 있습니다. 이를 그대로 사용 하면 holograms 표시 될 수 있습니다. 이 동작을 방지 하기 위해 이전 및 현재 포커스 지점의 보간을 권장 합니다.

## <a name="next-steps"></a>다음 단계

* [서버 쪽 성능 쿼리](performance-queries.md)
