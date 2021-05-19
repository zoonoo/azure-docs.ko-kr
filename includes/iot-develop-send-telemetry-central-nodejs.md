---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6499ca664e56ef6484a5d83d78fee5e3b6c0e800
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776877"
---
## <a name="prerequisites"></a>사전 요구 사항
- [Node.js](https://nodejs.org/) 버전 6 이상 버전을 확인하려면 콘솔 애플리케이션에서 `node --version`을 실행합니다.
- [Git](https://git-scm.com/downloads)
- Linux 또는 Windows에서 이 빠른 시작을 실행할 수 있습니다. 셸 명령은 표준 Linux 경로 구분 기호 `/`를 사용합니다. Windows를 사용하는 경우 이 구분 기호를 Windows 경로 구분 기호 `\`로 바꿉니다.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 로컬 환경을 구성하고, Azure IoT Node.js 디바이스 SDK를 설치하고, 시뮬레이션된 온도 컨트롤러를 만드는 샘플을 실행합니다.

### <a name="configure-your-environment"></a>환경 구성

1. Windows CMD, PowerShell 또는 Bash를 사용하여 콘솔을 엽니다.

1. 콘솔에 적합한 명령을 사용하여 다음 환경 변수를 설정합니다. 시뮬레이션된 디바이스는 이 값을 사용하여 IoT Central에 연결합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 이전에 저장한 디바이스 연결 값을 사용합니다.

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

### <a name="install-the-sdk-and-samples"></a>SDK 및 샘플을 설치합니다.

1. Azure IoT Node.js 디바이스 SDK를 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 샘플 디렉터리로 이동합니다.
    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```
1. Azure IoT Node.js SDK 및 필요한 종속성을 설치합니다.
    ```console
    npm install
    ```

### <a name="run-the-code"></a>코드 실행

1. 콘솔에서 SDK에서 다음 코드 샘플을 실행합니다. 샘플은 자동 온도 조절기 센서가 있는 시뮬레이션된 온도 컨트롤러를 만듭니다.
    ```console
    node pnpTemperatureController.js
    ```

    시뮬레이션된 디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 표시됩니다. 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```