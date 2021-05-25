---
title: 서버 크기
description: 할당할 수 있는 고유한 서버 크기를 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594403"
---
# <a name="server-sizes"></a>서버 크기

Azure Remote Rendering은 `Standard`와 `Premium`의 두 가지 서버 구성에서 사용할 수 있습니다.

## <a name="polygon-limits"></a>다각형 제한

`Standard` 크기 서버를 사용하는 Remote Rendering의 최대 장면 크기는 2000만개의 다각형입니다. `Premium` 크기를 사용하는 Remote Rendering에는 고정 최댓값이 적용되지 않지만 콘텐츠가 서비스의 렌더링 기능을 초과하는 경우 성능이 저하될 수 있습니다.

'Standard' 서버 크기의 렌더러가 이 제한에 도달하면 렌더링을 바둑판 배경으로 전환합니다.

![도구 메뉴를 사용하여 흑백 사각형 그리드를 보여주는 스크린샷입니다.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>서버 크기 지정

원하는 서버 구성 유형은 렌더링 세션 초기화 시간에 지정해야 합니다. 실행 중인 세션 내에서 변경할 수 없습니다. 다음 코드 예제에서는 서버 크기를 지정해야 하는 위치를 보여 줍니다.

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

[예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)의 경우 원하는 서버 크기를 `arrconfig.json` 파일 내에 지정해야 합니다.

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>렌더러에서 다각형 수를 평가하는 방법

제한 테스트에 고려되는 다각형 수는 실제로 렌더러에 전달되는 다각형 수입니다. 이 기하 도형은 일반적으로 인스턴스화된 모든 모델의 합계이지만 예외도 있습니다. 다음 기하 도형은 **포함되지 않습니다**.
* 완전히 뷰 절두체 밖에 있는 로드된 모델 인스턴스.
* [계층 구조 상태 재정의 구성 요소](../overview/features/override-hierarchical-state.md)를 사용하여 숨김으로 전환된 모델 또는 모델 파트.

따라서 모든 단일 모델에 대한 한도에 가까운 다각형 수를 사용하여 여러 모델을 로드하는 `standard` 크기를 대상으로 하는 애플리케이션을 작성할 수 있습니다. 애플리케이션에서 한 번에 하나의 단일 모델만 표시하는 경우에는 바둑판이 트리거되지 않습니다.

### <a name="how-to-determine-the-number-of-polygons"></a>다각형 수를 결정하는 방법

`standard` 구성 크기의 예산 한도에 영향을 주는 모델 또는 장면의 다각형 수를 결정하는 방법에는 다음 두 가지가 있습니다.
* 모델 변환 측에서 [변환 출력 json 파일](../how-tos/conversion/get-information.md)을 검색하고 [*inputStatistics* 섹션](../how-tos/conversion/get-information.md#the-inputstatistics-section)에서 `numFaces` 항목을 확인합니다.
* 애플리케이션에서 동적 콘텐츠를 처리하는 경우 렌더링된 다각형의 수를 런타임 중에 동적으로 쿼리할 수 있습니다. [성능 평가 쿼리](../overview/features/performance-queries.md#performance-assessment-queries)를 사용하여 `FrameStatistics` 구조체에서 `polygonsRendered` 멤버를 확인합니다. 렌더러가 다각형 제한에 도달하면 `PolygonsRendered` 필드가 `bad`로 설정됩니다. 이 비동기 쿼리 후 사용자 작업을 수행할 수 있도록 바둑판 배경은 약간의 지연과 함께 항상 페이드 인됩니다. 예를 들어 사용자 작업은 모델 인스턴스를 숨기거나 삭제할 수 있습니다.

## <a name="pricing"></a>가격 책정

각 구성 유형의 가격 책정에 대한 자세한 내용은 [Remote Rendering 가격 책정](https://azure.microsoft.com/pricing/details/remote-rendering) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
* [모델 변환](../how-tos/conversion/model-conversion.md)

