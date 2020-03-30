---
title: 로깅 및 진단
description: Azure 공간 앵커에서 로깅 및 진단을 생성하고 검색하는 방법에 대한 자세한 설명입니다.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270121"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 공간 앵커의 로깅 및 진단

Azure 공간 앵커는 앱 개발에 유용한 표준 로깅 메커니즘을 제공합니다. 공간 앵커 진단 로깅 모드는 디버깅에 대한 자세한 정보가 필요할 때 유용합니다. 진단 로깅은 환경의 이미지를 저장합니다.

## <a name="standard-logging"></a>표준 로깅
공간 앵커 API에서 로깅 메커니즘을 구독하여 응용 프로그램 개발 및 디버깅에 유용한 로그를 얻을 수 있습니다. 표준 로깅 API는 장치 디스크에 환경 사진을 저장하지 않습니다. SDK는 이러한 로그를 이벤트 콜백으로 제공합니다. 이러한 로그를 응용 프로그램의 로깅 메커니즘에 통합하는 것은 귀하가 해야 합니다.

### <a name="configuration-of-log-messages"></a>로그 메시지 구성
사용자에게는 두 가지 콜백이 있습니다. 다음 샘플에서는 세션을 구성하는 방법을 보여 주며 있습니다.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>이벤트 및 속성

이러한 이벤트 콜백은 세션에서 로그 및 오류를 처리하기 위해 제공됩니다.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): 런타임에서 수신할 이벤트에 대한 세부 수준을 지정합니다.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): 표준 디버그 로그 이벤트를 제공합니다.
- [오류](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): 런타임에서 오류로 간주하는 로그 이벤트를 제공합니다.

## <a name="diagnostics-logging"></a>진단 로깅

공간 앵커는 로깅을 위한 표준 작동 모드 외에도 진단 모드도 있습니다. 진단 모드는 환경의 이미지를 캡처하여 디스크에 기록합니다. 이 모드를 사용하여 예측 가능한 앵커를 찾지 못하는 것과 같은 특정 종류의 문제를 디버깅할 수 있습니다. 진단 로깅을 사용하여 특정 문제를 재현할 수 있습니다. 그런 다음 사용하지 않도록 설정합니다. 앱을 정상적으로 실행할 때는 진단을 사용하도록 설정하지 마십시오.

Microsoft와의 지원 상호 작용 중에 추가 조사를 위해 진단 번들을 제출할 의향이 있는지 Microsoft 담당자가 요청할 수 있습니다. 이 경우 진단 번들을 제출할 수 있도록 진단을 사용하도록 설정하고 문제를 재현할 수 있습니다.

Microsoft 담당자의 사전 승인 없이 진단 로그를 Microsoft에 제출하면 제출이 응답되지 않습니다.

다음 섹션에서는 진단 모드를 활성화하는 방법과 진단 로그를 Microsoft에 제출하는 방법을 보여 주며, 진단 로그를 제출하는 방법을 보여 준다.

### <a name="enable-diagnostics-logging"></a>진단 로깅 사용

진단 로깅에 대한 세션을 사용하도록 설정하면 세션의 모든 작업에 로컬 파일 시스템에 해당 진단 로깅이 있습니다. 로깅하는 동안 환경의 이미지가 디스크에 저장됩니다.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>진단 번들 제출

다음 코드 조각은 진단 번들을 Microsoft에 제출하는 방법을 보여 주며 있습니다. 이 번들에는 진단을 활성화한 후 세션에서 캡처한 환경 이미지가 포함됩니다.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>진단 번들의 일부
진단 번들에는 다음 정보가 포함될 수 있습니다.

- **키프레임 이미지**: 진단이 활성화된 동안 세션 중에 캡처한 환경 이미지입니다.
- **로그**: 런타임에 의해 기록된 로그 이벤트입니다.
- **세션 메타데이터**: 세션을 식별하는 메타데이터입니다.
