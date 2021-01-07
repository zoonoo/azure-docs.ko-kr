---
title: 카메라
description: 카메라 설정 및 사용 사례에 대해 설명 합니다.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: fc82d046caa3663cffcda585258642813ab3a7d8
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207260"
---
# <a name="camera"></a>카메라

로컬 및 원격으로 렌더링 된 콘텐츠를 원활 하 게 합성 하려면 서버와 클라이언트 간에 다양 한 카메라 속성을 동기화 상태로 유지 해야 합니다. 카메라 변환 및 프로젝션이 가장 특히 유용 합니다. 예를 들어 로컬로 렌더링 된 콘텐츠는 최신 원격 프레임이 렌더링 된 것과 동일한 카메라 변환과 프로젝션을 사용 해야 합니다.

![로컬 및 원격 카메라](./media/camera.png)

위의 이미지에서 로컬 카메라가 서버에서 보낸 원격 프레임과 비교 하 여 이동 했습니다. 따라서 로컬 콘텐츠는 원격 프레임과 동일한 관점에서 렌더링 되어야 하 고 마지막으로 결과 이미지는 로컬 카메라 공간으로 다시 투영 됩니다 .이에 대해서는 [후기 단계 다시 프로젝션](late-stage-reprojection.md)에 자세히 설명 되어 있습니다.

원격 렌더링과 로컬 렌더링의 지연 시간은 이러한 설정의 변경 내용이 지연과 함께 적용 됨을 의미 합니다. 따라서 새 값은 동일한 프레임으로 즉시 사용할 수 없습니다.

카메라 설정에서 근거리 및 원거리 평면 거리를 설정할 수 있습니다. HoloLens 2에서 나머지 변환 및 프로젝션 데이터는 하드웨어에 의해 정의 됩니다. [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md)을 사용 하는 경우 해당 함수 입력을 통해 설정 됩니다 `Update` .

변경 된 값은이를 사용 하 여 렌더링 된 첫 번째 원격 프레임이 도착 하는 즉시 로컬로 사용할 수 있습니다. HoloLens 2에서 렌더링에 사용할 데이터는 다른 holographic 응용 프로그램에서와 마찬가지로 *HolographicFrame* 에서 제공 합니다. [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md)에서 함수 출력을 통해 검색 됩니다 `Update` .

## <a name="camera-settings"></a>카메라 설정

카메라 설정에서 다음 속성을 변경할 수 있습니다.

**근거리 및 원거리 평면:**

잘못 된 범위를 설정할 수 없도록 하기 위해 **NearPlane** 및 **FarPlane** 속성은 읽기 전용이 고, 범위를 변경 하기 위해 별도의 함수 **SetNearAndFarPlane** 가 있습니다. 이 데이터는 프레임의 끝에 있는 서버로 전송 됩니다.

> [!IMPORTANT]
> Unity에서이는 주 카메라와 far 비행기를 변경할 때 자동으로 처리 됩니다.

**Enabledepth**:

디버깅을 위해 원격 이미지의 깊이 버퍼 쓰기를 사용 하지 않도록 설정 하는 것이 유용한 경우도 있습니다. 깊이를 사용 하지 않도록 설정 하면 서버에서 깊이 데이터를 전송 하지 않으므로 대역폭이 줄어듭니다.

> [!TIP]
> Unity에서 편집기 UI에서이 기능을 설정/해제 하는 데 사용할 수 있는 **EnableDepthComponent** 라는 디버그 구성 요소가 제공 됩니다.

카메라 설정을 변경 하는 작업은 다음과 같이 수행할 수 있습니다.

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API 설명서

* [C # CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C + + CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C # GraphicsBindingSimD3d11 함수](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C + + GraphicsBindingSimD3d11:: Update 함수](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>다음 단계

* [그래픽 바인딩](../../concepts/graphics-bindings.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)