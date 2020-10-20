---
title: Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용(C#) | Microsoft Docs
description: C#을 사용하여 Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 상호 작용합니다.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: ec38e0849b7f4c1a0ca98d75d4c6c82908c1e16e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945379"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>빠른 시작: 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용(C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 플러그 앤 플레이를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 C#을 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows에서 이 빠른 시작을 완료하려면 개발 머신에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/)
* [Git](https://git-scm.com/download/)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>샘플 코드를 사용하여 SDK 리포지토리 복제

[빠른 시작: Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub(C#)에 연결](quickstart-connect-device-csharp.md)을 완료한 경우 이미 리포지토리를 복제한 것입니다.

C#용 Azure IoT 샘플 GitHub 리포지토리에서 샘플을 복제합니다. 선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [.NET용 Microsoft Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 복제합니다.

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 빠른 시작에서는 C#으로 작성한 샘플 자동 온도 조절 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. Visual Studio 2019에서 *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* 프로젝트 파일을 엽니다.

1. Visual Studio에서 **프로젝트 > 자동 온도 조절기 속성 > 디버그**로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [환경 설정](set-up-environment.md)을 완료했을 때 기록한 값 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [환경 설정](set-up-environment.md)을 완료했을 때 기록한 값 |

1. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 이 메시지는 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었음을 나타냅니다. 이 Visual Studio의 인스턴스를 닫지 마세요. 서비스 샘플이 작동하는지 확인하는 데 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

[IoT 플러그 앤 플레이 빠른 시작 및 자습서](set-up-environment.md)에서는 IoT 허브 및 디바이스에 연결하도록 샘플을 구성하는 두 가지 환경 변수를 만들었습니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 기록한 IoT 허브 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

이 빠른 시작에서는 C#에서 샘플 IoT 솔루션을 사용하여 방금 설정한 샘플 디바이스와 상호 작용합니다.

1. Visual Studio의 다른 인스턴스에서 *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* 프로젝트를 엽니다.

1. Visual Studio에서 **프로젝트 > 자동 온도 조절기 속성 > 디버그**로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | [환경 설정](set-up-environment.md)을 완료했을 때 기록한 값 |

1. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

### <a name="get-device-twin"></a>디바이스 쌍 가져오기

다음 코드 조각에서는 서비스 애플리케이션이 디바이스 쌍을 검색하는 방법을 보여줍니다.

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> 이 샘플에서는 **IoT Hub 서비스 클라이언트**에서 **Microsoft.Azure.Devices.Client** 네임스페이스를 사용합니다. 디지털 쌍 API를 비롯한 API에 대한 자세한 내용은 [서비스 개발자 가이드](concepts-developer-guide-service.md)를 참조하세요.

이 코드는 다음과 같은 출력을 생성합니다.

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

다음 코드 조각에서는 *패치*를 사용하여 디바이스 쌍을 통해 속성을 업데이트하는 방법을 보여줍니다.

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

이 코드는 서비스에서 `targetTemperature` 속성을 업데이트할 때 디바이스에서 다음과 같은 출력을 생성합니다.

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>명령 호출

다음 코드 조각에서는 명령을 호출하는 방법을 보여 줍니다.

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

이 코드는 서비스에서 `getMaxMinReport` 명령을 호출할 때 디바이스에서 다음과 같은 출력을 생성합니다.

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT 솔루션에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 모델링 개발자 가이드](concepts-developer-guide-device-csharp.md)
