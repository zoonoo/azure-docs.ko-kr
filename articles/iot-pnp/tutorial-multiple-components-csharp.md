---
title: IoT 플러그 앤 플레이 미리 보기 샘플 C# 구성 요소 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: 여러 구성 요소를 사용하고 IoT 허브에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 C# 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552141"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>자습서: Windows에서 실행되는 IoT 플러그 앤 플레이 다중 구성 요소 디바이스 애플리케이션을 IoT Hub에 연결(C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

이 자습서에서는 구성 요소 및 루트 인터페이스를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT Hub에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 C#으로 작성되었으며 C#용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 자습서의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 _IoT 허브 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 두었다가 이 자습서의 뒷부분에서 사용합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 _디바이스 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 두었다가 이 자습서의 뒷부분에서 사용합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>코드 다운로드

이 자습서에서는 Azure IoT Hub Device C# SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C# SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-csharp) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>코드 빌드

Visual Studio 2019에서 **azureiot.sln** 솔루션 파일을 열고 **TemperatureController** 프로젝트를 시작 프로젝트로 설정합니다. **솔루션 탐색기**의 **iothub > 디바이스 > 샘플**에서 이 프로젝트 파일을 찾을 수 있습니다.

이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

**IOTHUB_DEVICE_CONNECTION_STRING**이라는 환경 변수를 만들어 이전에 기록한 디바이스 연결 문자열을 저장합니다.

Windows의 Visual Studio에서 코드 실행을 추적하려면 program.cs 파일의 `main` 함수에 중단점을 추가합니다.

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 [여러 구성 요소](concepts-components.md)를 사용합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 `CreateFromConnectionString` 메서드를 사용하여 IoT 허브에 연결합니다. 디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

모델 ID는 다음 코드 조각과 같이 코드에 저장됩니다.

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

디바이스가 IoT 허브에 연결되면 코드에 명령 처리기를 등록합니다. `reboot` 명령은 루트 인터페이스에 정의되어 있습니다. `getMaxMinReport` 명령은 두 개의 온도 조절기 구성 요소 각각에 정의되어 있습니다.

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

두 개의 온도 조절기 구성 요소에 원하는 속성 업데이트에 대한 별도의 처리기가 있습니다.

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

샘플 코드는 각 온도 조절기 구성 요소에서 원격 분석을 보냅니다.

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

`SendTemperature` 메서드는 `PnpHhelper` 클래스를 사용하여 각 구성 요소에 대한 메시지를 만듭니다.

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` 클래스에는 여러 구성 요소 모델에서 사용할 수 있는 다른 예제 메서드가 포함되어 있습니다.

Azure IoT 탐색기 도구를 사용하여 두 개의 온도 조절기 구성 요소의 원격 분석 및 속성을 볼 수 있습니다.

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT 탐색기의 여러 구성 요소 디바이스":::

Azure IoT 탐색기 도구를 사용하여 두 개의 온도 조절기 구성 요소 중 하나 또는 루트 인터페이스에서 명령을 호출할 수도 있습니다.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 디바이스를 구성 요소와 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드](concepts-developer-guide.md)
