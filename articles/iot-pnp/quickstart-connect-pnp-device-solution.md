---
title: Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 | Microsoft Docs
description: Node.js를 사용하여 Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용합니다.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881592"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>빠른 시작: 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용

IoT 플러그 앤 플레이 미리 보기를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Node.js를 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

[nodejs.org](https://nodejs.org)에서 Node.js를 다운로드하여 설치합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT Hub 준비

또한 이 빠른 시작을 완료하려면 Azure 구독의 Azure IoT Hub가 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

다음과 같이 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

다음 명령을 실행하여 IoT Hub에 디바이스 ID를 만듭니다. **YourIoTHubName** 및 **YourDevice**를 실제 이름으로 바꿉니다. IoT Hub가 없는 경우 [다음 지침](../iot-hub/iot-hub-create-using-cli.md)에 따라 새로 만듭니다.

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

다음 명령을 실행하여 허브의 _IoT Hub 연결 문자열_을 가져옵니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>디바이스 연결

이 빠른 시작에서는 Node.js에서 IoT 플러그 앤 플레이 디바이스로 작성된 샘플 환경 센서를 사용합니다. 다음 지침은 디바이스를 설치하고 실행하는 방법을 보여줍니다.

1. 다음과 같이 GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 터미널에서 복제된 리포지토리의 루트 폴더로 이동하고, **/azure-iot-samples-node/digital-twins/Quickstarts/Device** 폴더로 이동한 다음, 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 다음과 같이 _디바이스 연결 문자열_을 구성합니다.

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd/sh
    node sample_device.js
    ```

1. 디바이스에서 원격 분석 데이터와 해당 속성을 전송했다는 메시지가 표시됩니다. 이제 디바이스에서 명령 및 속성 업데이트를 받을 수 있습니다. 이 터미널을 닫지 마세요. 나중에 서비스 샘플이 작동하는지 확인할 때 필요합니다.

## <a name="build-the-solution"></a>솔루션을 빌드합니다.

이 빠른 시작에서는 Node.js에서 샘플 IoT 솔루션을 사용하여 샘플 디바이스와 상호 작용합니다.

1. 또 다른 터미널을 엽니다. 복제된 리포지토리의 폴더로 이동하고, **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 다음과 같이 _허브 연결 문자열_을 구성합니다.

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>속성 읽기

1. 터미널에서 디바이스를 연결하면 다음과 같은 메시지가 표시됩니다.

    ```cmd/sh
    reported state property as online
    ```

1. **get_digital_twin.js** 파일을 엽니다. `deviceID`를 해당 디바이스 ID로 바꾸고 파일을 저장합니다.

1. 서비스 샘플을 실행하기 위해 연 터미널로 이동하여 다음 명령을 실행합니다.

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 다음과 같이 출력의 _environmentalSensor_ 구성 요소 아래에 동일한 상태가 보고됩니다.

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>쓰기 가능 속성 업데이트

1. **update_digital_twin_property.js** 파일을 엽니다.

1. 파일의 시작 부분에는 대문자 자리 표시자로 정의된 상수 세트가 있습니다. **deviceID**를 실제 디바이스 ID로 바꾸고, 상수를 다음 값으로 업데이트하고, 파일을 저장합니다.

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. 서비스 샘플을 실행하기 위해 연 터미널로 이동하고, 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. 터미널에는 디바이스와 연결된 디지털 쌍 정보가 표시됩니다. _environmentalSensor_ 구성 요소를 찾습니다. 새로운 밝기 값 60이 표시됩니다.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. _서비스_ 터미널로 돌아가서 아래 명령을 다시 실행하여 속성이 업데이트되었는지 확인합니다.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. 출력의 environmentalSensor 구성 요소 아래에 업데이트된 밝기 값이 보고됩니다. 참고: 디바이스가 업데이트를 완료할 때까지 잠시 시간이 걸릴 수 있습니다. 디바이스에서 속성 업데이트를 실제로 처리할 때까지 이 단계를 반복할 수 있습니다.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>명령 호출

1. **invoke_command.js** 파일을 엽니다.

1. 파일의 시작 부분에서 `deviceID`를 실제 디바이스 ID로 바꿉니다. 상수를 다음 값으로 업데이트한 다음, 파일을 저장합니다.

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. 서비스 샘플을 실행하기 위해 연 터미널로 이동합니다. 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd/sh
    node invoke_command.js
    ```

1. 터미널에서 다음과 같이 출력되면 성공입니다.

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. _디바이스_ 터미널로 이동하면 명령이 승인된 것을 볼 수 있습니다.

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>리소스 정리

이후 문서를 계속 진행하려면 이 빠른 시작에서 사용한 리소스를 그대로 유지하세요. 그렇지 않으면 추가 요금이 발생하지 않도록 이 빠른 시작에서 만든 리소스를 삭제하세요.

허브 및 등록된 디바이스를 삭제하려면 Azure CLI를 사용하여 다음 단계를 완료합니다.

```azurecli-interactive
az group delete --name <Your group name>
```

IoT Hub에 등록한 디바이스를 삭제하려면 Azure CLI를 사용하여 다음 단계를 완료합니다.

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT 솔루션에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: 디바이스에 연결 및 상호 작용](howto-develop-solution.md)
