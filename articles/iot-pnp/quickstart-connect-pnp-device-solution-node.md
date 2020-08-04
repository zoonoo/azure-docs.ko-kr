---
title: 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 - Node.js | Microsoft Docs
description: Node.js를 사용하여 Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용합니다.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7f86ae8ffefa88fe1cdfa429b7de4671422839ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044001"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>빠른 시작: 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용(Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 플러그 앤 플레이 미리 보기를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Node.js를 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 개발 머신에 Node.js가 필요합니다. [nodejs.org](https://nodejs.org)에서 여러 플랫폼에 권장하는 최신 버전을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브의 _IoT 허브 연결 문자열_을 가져옵니다(나중에 사용하기 위해 참고).

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 빠른 시작에서는 Node.js에서 IoT 플러그 앤 플레이 디바이스로 작성된 샘플 환경 센서를 사용합니다. 다음 지침은 디바이스를 설치하고 실행하는 방법을 보여줍니다.

1. 원하는 디렉터리에서 터미널 창을 엽니다. 다음 명령을 실행하여 [Node.js용 Azure IoT 샘플](https://github.com/azure-samples/azure-iot-samples-node) GitHub 리포지토리를 다음 위치에 복제합니다.

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 이 터미널 창은 이제 _디바이스_ 터미널로 사용됩니다. 복제된 리포지토리의 폴더로 이동하고, **/azure-iot-samples-node/digital-twins/Quickstarts/Device** 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 다음과 같이 _디바이스 연결 문자열_을 구성합니다.

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd/sh
    node sample_device.js
    ```

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었다는 뜻입니다. 이 터미널을 닫지 마세요. 나중에 서비스 샘플이 작동하는지 확인할 때 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

이 빠른 시작에서는 Node.js에서 샘플 IoT 솔루션을 사용하여 샘플 디바이스와 상호 작용합니다.

1. 또 다른 터미널 창을 엽니다. 이 터미널 창이 _서비스_ 터미널이 됩니다. 복제된 리포지토리의 폴더로 이동하고, **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 서비스가 연결할 수 있도록 _IoT 허브 연결 문자열_을 구성합니다.

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>속성 읽기

1. 터미널에서 _디바이스_를 연결한 경우 디바이스가 온라인 상태임을 나타내는 다음 메시지가 표시됩니다.

    ```cmd/sh
    reported state property as online
    ```

1. **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 폴더에서 **get_digital_twin.js** 파일을 엽니다. `<DEVICE_ID_GOES_HERE>` 자리 표시자를 디바이스 ID로 바꾸고 파일을 저장합니다.

1. _서비스_ 터미널로 이동한 후 다음 명령을 사용하여 디바이스 정보를 읽는 샘플을 실행합니다.

    ```cmd/sh
    node get_digital_twin.js
    ```

1. _서비스_ 터미널 출력에서 `environmentalSensor` 구성 요소로 스크롤합니다. `state` 속성이 _온라인_으로 보고된 것을 볼 수 있습니다.

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>쓰기 가능 속성 업데이트

1. **update_digital_twin_property.js** 파일을 엽니다.

1. 파일의 시작 부분에는 대문자 자리 표시자로 정의된 상수 세트가 있습니다. `<DEVICE_ID_GOES_HERE>` 자리 표시자를 실제 디바이스 ID로 바꾸고, 나머지 상수를 다음 값으로 업데이트하고, 파일을 저장합니다.

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. _서비스_ 터미널로 이동한 후 다음 명령을 사용하여 속성을 업데이트하는 샘플을 실행합니다.

    ```cmd/sh
    node update_digital_twin_property.js
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
            "value": "online"
          }
        }
      }
    }
    ```

1. _디바이스_ 터미널로 이동하면 디바이스에서 업데이트를 수신하는 것을 볼 수 있습니다.

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. _서비스_ 터미널로 돌아가서 아래 명령을 다시 실행하여 디바이스 정보를 다시 가져오고, 속성이 업데이트되었는지 확인합니다.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. _서비스_ 터미널 출력의 `environmentalSensor` 구성 요소 아래에 업데이트된 밝기 값이 보고됩니다. 참고: 디바이스가 업데이트를 완료할 때까지 잠시 시간이 걸릴 수 있습니다. 디바이스에서 속성 업데이트를 실제로 처리할 때까지 이 단계를 반복할 수 있습니다.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>명령 호출

1. **invoke_command.js** 파일을 엽니다.

1. 파일의 시작 부분에서 `<DEVICE_ID_GOES_HERE>` 자리 표시자를 실제 디바이스 ID로 바꿉니다. 나머지 상수를 다음 값으로 업데이트한 다음, 파일을 저장합니다.

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. _서비스_ 터미널로 이동합니다. 다음 명령을 사용하여 명령을 호출하는 샘플을 실행합니다.

    ```cmd/sh
    node invoke_command.js
    ```

1. _서비스_ 터미널의 출력에는 다음과 같은 확인 메시지가 표시됩니다.

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. _디바이스_ 터미널로 이동하면 명령이 승인된 것을 볼 수 있습니다.

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT 솔루션에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: 디바이스에 연결 및 상호 작용](howto-develop-solution.md)
