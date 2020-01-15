---
title: Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 | Microsoft Docs
description: C#(.NET)을 사용하여 Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용합니다.
author: baanders
ms.author: baanders
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9e8bc6c4ad7ed852ddaae2e193b91887fcd92e47
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550777"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>빠른 시작: 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용(C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 플러그 앤 플레이 미리 보기를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 C#(.NET)을 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 개발 머신에 .NET Core(2.x.x 또는 3.x.x)를 설치해야 합니다. [.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core/)에서 여러 플랫폼에 대한 .NET Core SDK의 기본 버전을 다운로드할 수 있습니다.

로컬 터미널 창에서 다음 명령을 실행하여 개발 머신에 있는 .NET 버전을 확인할 수 있습니다. 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브의 _IoT 허브 연결 문자열_을 가져옵니다(나중에 사용하기 위해 참고).

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 빠른 시작에서는 C#으로 작성된 샘플 환경 센서를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 다음 지침은 디바이스를 설치하고 실행하는 방법을 보여줍니다.

1. 원하는 디렉터리에서 터미널 창을 엽니다. 다음 명령을 실행하여 [C#(.NET)용 Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 다음 위치에 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. 이 터미널 창은 이제 _디바이스_ 터미널로 사용됩니다. 복제된 리포지토리의 폴더로 이동하고, **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** 폴더로 이동합니다.

1. 다음과 같이 _디바이스 연결 문자열_을 구성합니다.

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 다음 명령을 사용하여 필요한 패키지를 빌드하고 샘플을 실행합니다.

    ```cmd\sh
        dotnet run
    ```

1. 디바이스가 성공적으로 등록되고 클라우드에서 업데이트를 기다린다는 메시지가 표시됩니다. 이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었고, 원격 분석 데이터를 허브로 보내기 시작했다는 것을 나타냅니다. 이 터미널을 닫지 마세요. 나중에 서비스 샘플이 작동하는지 확인할 때 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

이 빠른 시작에서는 C# 형식의 샘플 IoT 솔루션을 사용하여 샘플 디바이스와 상호 작용합니다.

1. 또 다른 터미널 창을 엽니다. 이 터미널 창이 _서비스_ 터미널이 됩니다. 복제된 리포지토리의 폴더로 이동하고, **/azure-iot-samples-csharp/digitaltwin/Samples/service** 폴더로 이동합니다.

1. 다음 두 가지 모두에 연결할 수 있도록 _IoT 허브 연결 문자열_과 _디바이스 ID_를 구성합니다.

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>속성 읽기

1. 터미널에서 _디바이스_를 연결한 경우 디바이스가 온라인 상태임을 나타내는 다음 메시지가 표시됩니다.

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. _서비스_ 터미널로 이동한 후 다음 명령을 사용하여 디바이스 정보를 읽는 샘플을 실행합니다.

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. _서비스_ 터미널 출력에서 `environmentalSensor` 구성 요소로 스크롤합니다. 디바이스가 온라인 상태인지 여부를 나타내는 데 사용되는 `state` 속성이 _true_로 보고되었는지 확인합니다.

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>쓰기 가능 속성 업데이트

1. _서비스_ 터미널로 이동하고 다음 변수를 설정하여 업데이트할 속성을 정의합니다.
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. 다음 명령을 사용하여 속성을 업데이트하는 샘플을 실행합니다.

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. _서비스_ 터미널 출력에는 업데이트된 디바이스 정보가 표시됩니다. `environmentalSensor` 구성 요소로 스크롤하여 새 밝기 값이 42인지 확인합니다.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. _디바이스_ 터미널로 이동하면 디바이스에서 업데이트를 수신하는 것을 볼 수 있습니다.

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. _서비스_ 터미널로 돌아가서 아래 명령을 다시 실행하여 디바이스 정보를 다시 가져오고, 속성이 업데이트되었는지 확인합니다.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. _서비스_ 터미널 출력의 `environmentalSensor` 구성 요소 아래에 업데이트된 밝기 값이 보고됩니다. 참고: 디바이스가 업데이트를 완료할 때까지 잠시 시간이 걸릴 수 있습니다. 디바이스에서 속성 업데이트를 실제로 처리할 때까지 이 단계를 반복할 수 있습니다.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>명령 호출

1. _서비스_ 터미널로 이동하고 다음 변수를 설정하여 호출할 명령을 정의합니다.
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. 다음 명령을 사용하여 명령을 호출하는 샘플을 실행합니다.

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. _서비스_ 터미널의 출력에는 다음과 같은 확인 메시지가 표시됩니다.

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. _디바이스_ 터미널로 이동하면 명령이 승인된 것을 볼 수 있습니다.

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT 솔루션에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: 디바이스에 연결 및 상호 작용](howto-develop-solution.md)
