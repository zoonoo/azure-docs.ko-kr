---
title: Azure IoT Hub에 원격 분석 전송 빠른 시작(Python) | Microsoft Docs
description: 이 빠른 시작에서는 샘플 Python 애플리케이션을 실행하여 시뮬레이션된 원격 분석을 IoT Hub로 보내고, 유틸리티를 사용하여 원격 분석을 IoT Hub에서 읽습니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/17/2019
ms.openlocfilehash: 73722a7e5581d5e6275ec23f31351c2e4d4561ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675365"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>빠른 시작: 디바이스에서 IoT Hub로 원격 분석을 보내고 백 엔드 애플리케이션(Python)으로 읽습니다.

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

이 빠른 시작에서는 시뮬레이션된 디바이스 애플리케이션에서 Azure IoT Hub를 통해 백 엔드 애플리케이션으로 원격 분석 처리를 위해 보냅니다. IoT Hub는 스토리지 또는 처리를 위해 IoT 디바이스에서 클라우드로 다량의 원격 분석 데이터를 수집할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작은 미리 작성된 Python 애플리케이션을 사용하여 원격 분석을 보내고 CLI 유틸리티를 사용하여 허브에서 원격 분석을 읽기 위한 것입니다. 이 두 애플리케이션을 실행하기 전에 IoT 허브를 만들고 허브에 디바이스를 등록합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7 이상](https://www.python.org/downloads/). 지원되는 다른 버전의 Python은 [Azure IoT 디바이스 기능](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)을 참조하세요.

* [샘플 Python 프로젝트](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* 방화벽에서 포트 8883이 열려 있습니다. 이 빠른 시작의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT 확장 추가

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IoT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    **MyPythonDevice**: 등록 중인 디바이스의 이름입니다. 표시된 대로 **MyPythonDevice**를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Azure Cloud Shell에서 다음 명령을 실행하여 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="send-simulated-telemetry"></a>시뮬레이션된 원격 분석 전송

시뮬레이션된 디바이스 애플리케이션은 IoT 허브의 디바이스 관련 엔드포인트에 연결하고 시뮬레이션된 온도 및 습도 원격 분석을 전송합니다.

1. 로컬 터미널 창에서 Python 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.py** 파일을 엽니다.

    `CONNECTION_STRING` 변수의 값을 이전에 기록해 둔 디바이스 연결 문자열로 바꿉니다. 그런 다음, 변경 내용을 **SimulatedDevice.py**에 저장합니다.

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션을 실행합니다.

    ```cmd/sh
    python SimulatedDevice.py
    ```

    다음 스크린샷에서는 시뮬레이션된 디바이스 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

IoT Hub CLI 확장은 IoT Hub의 서비스 쪽 **이벤트** 엔드포인트에 연결할 수 있습니다. 이 확장은 시뮬레이트된 디바이스에서 보낸 디바이스-클라우드 메시지를 받습니다. IoT Hub 백 엔드 애플리케이션은 일반적으로 클라우드에서 실행되며 디바이스-클라우드 메시지를 수신하고 처리합니다.

Azure Cloud Shell에서 다음 명령을 실행하고, `YourIoTHubName`을 해당 IoT 허브 이름으로 바꿉니다.

```azurecli-interactive
az iot hub monitor-events --hub-name {YourIoTHubName} --device-id MyPythonDevice 
```

다음 스크린샷은 시뮬레이션된 디바이스가 허브에 보낸 원격 분석 데이터를 확장 프로그램이 수신하면서 출력한 내용을 보여 줍니다.

![백 엔드 애플리케이션 실행](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 IoT 허브를 설치하고, 디바이스를 등록하고 Python 애플리케이션을 사용하여 허브에 시뮬레이션된 원격 분석을 보내고, 간단한 백 엔드 애플리케이션을 사용하여 허브에서 원격 분석을 읽습니다.

백 엔드 애플리케이션에서 시뮬레이션된 디바이스를 제어하는 방법을 알아보려면 다음 빠른 시작을 계속합니다.

> [!div class="nextstepaction"]
> [빠른 시작: IoT 허브에 연결된 디바이스 제어](quickstart-control-device-python.md)
