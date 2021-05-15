---
title: Azure IoT Central에 디바이스 원격 분석 보내기 빠른 시작(Python)
description: 이 빠른 시작에서는 Python용 Azure IoT Hub 디바이스 SDK를 사용하여 원격 분석을 디바이스에서 IoT Central로 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 04/27/2021
ms.openlocfilehash: 2464d9d4e6a945620bdbf80728aa3e7df0e2776e
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226699"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>빠른 시작: 디바이스에서 Azure IoT Central로 원격 분석 보내기(Python)

**적용 대상**: [디바이스 애플리케이션 개발자](about-iot-develop.md#device-application-development)<br>
**완료 시간**: 12분

이 빠른 시작에서는 기본 IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 Azure IoT Central에서 디바이스를 관리하는 클라우드 애플리케이션을 만듭니다. 그런 다음, Azure IoT Python SDK를 사용하여 시뮬레이션된 자동 온도 조절 디바이스를 빌드하고, IoT Central에 연결하고, 원격 분석을 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항
- [Python 3.7](https://www.python.org/downloads/) 이상 Python 버전을 확인하려면 `python --version`을 실행합니다. 

[!INCLUDE [iot-develop-create-central-app-with-device](../../includes/iot-develop-create-central-app-with-device.md)]

## <a name="configure-a-simulated-device"></a>시뮬레이션된 디바이스 구성
이 섹션에서는 Python SDK 샘플을 사용하여 시뮬레이트된 자동 온도 조절 디바이스를 구성합니다.

1. Windows CMD, PowerShell 또는 Bash(Windows 또는 Linux용)를 사용하여 터미널을 엽니다. 터미널을 사용하여 Python SDK를 설치하고, 환경 변수를 업데이트하고, Python 코드 샘플을 실행합니다.

1. [Azure IoT Python SDK 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)을 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 샘플 디렉터리로 이동합니다.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Azure IoT Python SDK를 설치합니다.
    ```console
    pip3 install azure-iot-device
    ```

1. 시뮬레이션된 디바이스에서 IoT Central에 연결할 수 있도록 다음 환경 변수를 각각 설정합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 저장한 디바이스 연결 값을 사용합니다.

    **Windows CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Windows CMD의 경우 변수 값을 묶는 따옴표가 없습니다.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash(Linux 또는 Windows)**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

## <a name="send-telemetry"></a>원격 분석 전송
시스템을 구성하면 코드를 실행할 준비가 된 것입니다. 이 코드는 시뮬레이션된 자동 온도 조절기를 만들고, IoT Central 애플리케이션 및 디바이스 인스턴스에 연결하고, 원격 분석을 보냅니다.

1. 터미널에서 다음 코드 샘플을 실행합니다. 필요에 따라 Python IDE에서 Python 코드를 실행할 수 있습니다.
    ```console
    python temp_controller_with_thermostats.py
    ```

    시뮬레이션된 디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 표시됩니다. 
    
    ```output
    c:\azure-iot-sdk-python\azure-iot-device\samples\pnp>python temp_controller_with_thermostats.py
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```

1. IoT Central에서 **디바이스** 를 선택하고 디바이스 이름을 클릭한 다음, **원시 데이터** 탭을 선택합니다. 이 보기는 시뮬레이션된 디바이스의 원시 원격 분석을 표시합니다. 

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central 디바이스 원격 분석 원시 출력":::
    
    이제 디바이스가 안전하게 연결되어 원격 분석을 Azure IoT에 보내고 있습니다.
    
## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 만든 IoT Central 리소스가 더 이상 필요하지 않은 경우 이를 삭제할 수 있습니다. 필요에 따라 이 가이드의 설명서를 계속 따르려는 경우 만든 애플리케이션을 유지하고 다른 샘플에 다시 사용할 수 있습니다.

Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure IoT Central을 사용하여 애플리케이션 및 디바이스를 만든 다음, Azure IoT Python SDK를 사용하여 시뮬레이션된 디바이스를 만들고 원격 분석을 보냈습니다. 또한 IoT Central을 사용하여 원격 분석을 모니터링했습니다.

다음 단계로, 디바이스를 호스팅하기 위한 솔루션으로 IoT Central을 탐색하고 Azure SDK Python 코드 샘플을 탐색합니다.

> [!div class="nextstepaction"]
> [IoT Central 애플리케이션 만들기](../iot-central/core/quick-deploy-iot-central.md)
> [!div class="nextstepaction"]
> [비동기 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)
> [!div class="nextstepaction"]
> [동기 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)
