---
title: 카메라
description: 카메라 설정 및 사용 사례를 설명합니다.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594132"
---
# <a name="camera"></a>카메라

로컬 및 원격으로 렌더링된 콘텐츠를 원활하게 합성하려면 서버와 클라이언트 간에 다양한 카메라 속성을 동기화 상태로 유지해야 합니다. 카메라 변환 및 프로젝션이 특히 중요합니다. 예를 들어 로컬로 렌더링된 콘텐츠는 최신 원격 프레임이 렌더링된 것과 동일한 카메라 변환과 프로젝션을 사용해야 합니다.

![로컬 및 원격 카메라](./media/camera.png)

위의 이미지에서 로컬 카메라가 서버에서 보낸 원격 프레임과 비교하여 이동했습니다. 따라서 로컬 콘텐츠는 원격 프레임과 동일한 큐브 뷰에서 렌더링되어야 하고 최종 결과 이미지는 로컬 카메라 공간으로 다시 투영됩니다. 자세한 내용은 [후기 단계 재투영](late-stage-reprojection.md)에 설명되어 있습니다.

원격 렌더링과 로컬 렌더링 간의 지연은 이러한 설정의 변경 내용이 지연으로 적용됨을 의미합니다. 따라서 새 값은 동일한 프레임으로 즉시 사용할 수 없습니다.

카메라 설정에서 근거리 및 원거리 평면 거리를 설정할 수 있습니다. HoloLens 2에서 나머지 변환 및 프로젝션 데이터는 하드웨어에 의해 정의됩니다. [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md)을 사용하는 경우 해당 `Update` 함수 입력을 통해 설정됩니다.

변경된 값은 이를 사용하여 렌더링된 첫 번째 원격 프레임이 도착하는 즉시 로컬로 사용할 수 있습니다. HoloLens 2에서 렌더링에 사용할 데이터는 다른 홀로그램 애플리케이션과 마찬가지로 *HolographicFrame* 에서 제공합니다. [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md)에서 `Update` 함수 출력을 통해 검색됩니다.

## <a name="camera-settings"></a>카메라 설정

카메라 설정에서 다음 속성을 변경할 수 있습니다.

**가까운 평면 및 먼 평면**

잘못된 범위를 설정할 수 없도록 **NearPlane** 및 **FarPlane** 속성은 읽기 전용이며 범위를 변경하기 위한 별도의 함수 **SetNearAndFarPlane** 이 있습니다. 이 데이터는 프레임의 끝에 있는 서버로 전송됩니다. 이러한 값을 설정하는 경우 **NearPlane** 은 **FarPlane** 보다 작아야 합니다. 그렇지 않으면 오류가 발생합니다.

> [!IMPORTANT]
> Unity에서 이 설정은 주 카메라의 가까운 평면과 먼 평면을 변경할 때 자동으로 처리됩니다.

**EnableDepth**:

디버깅을 위해 원격 이미지의 깊이 버퍼 쓰기를 사용하지 않도록 설정하는 것이 유용한 경우도 있습니다. 깊이를 사용하지 않도록 설정하면 서버에서 깊이 데이터를 전송하지 않으므로 대역폭이 줄어듭니다.

> [!TIP]
> Unity의 편집기 UI에서 이 기능을 설정/해제하는 데 사용할 수 있는 **EnableDepthComponent** 라는 디버그 구성 요소가 제공됩니다.

**InverseDepth**:

> [!NOTE]
> 이 설정은 `EnableDepth`가 `true`로 설정된 경우에만 중요합니다. 그렇지 않은 경우 이 설정은 영향을 미치지 않습니다.

깊이 버퍼는 일반적으로 [0;1]의 부동 소수점 범위에서 z 값을 기록합니다. 0은 가까운 평면 깊이를, 1은 먼 평면 깊이를 나타냅니다. 이 범위를 반전하여 [1;0] 범위에서 깊이 값을 기록할 수도 있습니다. 즉, 가까운 평면 깊이가 1이 되고 먼 평면 깊이가 0이 됩니다. 일반적으로 후자는 선형이 아닌 z 범위에서 부동 소수점 정밀도의 분포를 향상시킵니다.

> [!WARNING]
> 일반적인 접근 방식은 카메라 개체의 가까운 평면 및 먼 평면 값을 반전하는 것입니다. `CameraSettings`에서 이 방법을 시도하면 오류가 발생하여 Azure Remote Rendering에 실패합니다.

로컬 수준 버퍼로 원격 깊이를 올바르게 구성하기 위해 Azure Remote Rendering API는 로컬 렌더러의 깊이 버퍼 규칙에 대해 알고 있어야 합니다. 깊이 버퍼 범위가 [0;1]이면 이 플래그를 `false`로 둡니다. [1;0] 범위로 반전된 깊이 버퍼를 사용하는 경우 `InverseDepth` 플래그를 `true`로 설정합니다.

> [!NOTE]
> Unity의 경우 `RenderingConnection`에서 올바른 설정이 이미 적용되었으므로 수동 작업이 필요하지 않습니다.

카메라 설정을 변경하는 작업은 다음과 같이 수행할 수 있습니다.

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API 설명서

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Update 함수](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11::Update 함수](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>다음 단계

* [그래픽 바인딩](../../concepts/graphics-bindings.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)