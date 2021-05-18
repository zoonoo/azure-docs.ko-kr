---
title: 스테이지 공간
description: 스테이지 공간 설정 및 사용 사례에 대해 설명합니다.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594013"
---
# <a name="stage-space"></a>스테이지 공간

HoloLens 2와 같은 헤드 추적 데이터를 제공하는 디바이스에서 ARR을 실행하는 경우 헤드 포즈는 사용자 애플리케이션과 서버 모두에 전송됩니다. 헤드 변환을 정의하는 공간을 *스테이지 공간* 이라고 합니다.

로컬 및 원격 콘텐츠를 맞추려면 스테이지 공간과 표준 공간이 클라이언트와 서버 모두에서 동일하다고 가정합니다. 사용자가 카메라 위에 변환을 추가하기로 한 경우에는 로컬 및 원격 콘텐츠를 올바르게 정렬하기 위해 서버에도 전송해야 합니다.

스테이지 공간을 이동하는 일반적인 이유는 [표준 잠금 도구](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) 또는 실제의 다른 앵커링 기술일 뿐 아니라 VR에서 가상 캐릭터를 이동하기 때문입니다.

## <a name="stage-space-settings"></a>스테이지 공간 설정

> [!CAUTION]
> 실험적: 이 기능은 실험적이며 시간이 지남에 따라 변경됩니다. 따라서 최신 클라이언트 SDK 버전으로 업데이트하려면 코드를 업그레이드하는 추가 작업이 필요할 수 있습니다. 현재 구현에서는 스테이지 공간 원점을 변경할 때 잠시 동안 로컬/원격 콘텐츠 정렬을 중단합니다.
따라서 현재는 시간에 따라 약간만 변경되는 앵커와 같이 표준 잠금의 목적으로만 사용하기 위한 것입니다.

추가 변환이 스테이지 공간에 적용됨을 서버에 알리려면 표준 공간에서 위치 및 회전으로 정의된 원점을 전송해야 합니다. 이 설정은 *스테이지 공간 설정* 을 통해 액세스할 수 있습니다.

> [!IMPORTANT]
> [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md)에서 카메라의 표준 공간 위치는 사용자 애플리케이션에서 제공합니다. 이 경우 스테이지 공간 원점을 설정하는 것은 카메라 변환에 이미 적용되었으므로 건너뛰어야 합니다.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 스테이지 공간 스크립트

Unity 통합에서는 카메라의 부모 GameObject에 추가할 수 있는 **StageSpace** 라는 스크립트를 제공합니다. 이 스크립트가 스테이지 공간 원점 설정을 처리합니다.

## <a name="next-steps"></a>다음 단계

* [그래픽 바인딩](../../concepts/graphics-bindings.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)
