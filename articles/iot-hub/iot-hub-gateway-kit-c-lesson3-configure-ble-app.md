---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 단원 3: 샘플 앱 실행 | Microsoft Docs"
description: "BLE 샘플 응용 프로그램을 실행하여 BLE SensorTag 및 IoT Hub에서 데이터를 수신합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "ble 앱, 센서 모니터 앱, 센서 데이터 수집, 센서의 데이터, 센서 데이터를 클라우드로"
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>BLE 샘플 응용 프로그램 구성 및 실행

## <a name="what-you-will-do"></a>수행할 사항

- 샘플 리포지토리를 복제합니다. 
- SensorTag와 Intel NUC 간에 연결을 설정합니다. 
- Azure CLI를 사용하여 BLE(Bluetooth Low Energy) 샘플 응용 프로그램에 대한 IoT Hub 및 SensorTag 정보를 가져옵니다. 그런 다음 BLE 샘플 응용 프로그램 구성하고 실행합니다. 

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 문서에서는 다음에 대해 알아봅니다.

- BLE 샘플 응용 프로그램을 구성하고 실행하는 방법

## <a name="what-you-need"></a>필요한 항목

다음을 완료해야 합니다.

- [IoT Hub 만들기 및 SensorTag 등록](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>호스트 컴퓨터에 샘플 리포지토리 복제

샘플 리포지토리를 복제하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. Windows에서 명령 프롬프트 창을 열거나 macOS 또는 Ubuntu에서 터미널을 엽니다.
2. 다음 명령을 실행합니다.

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>SensorTag와 Intel NUC 간에 연결을 설정합니다.

연결을 설정하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. 다음 명령을 실행하여 Visual Studio Code에서 `config-gateway.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. 다음 코드 줄을 찾아 `[device hostname or IP address]`를 Intel NUC의 IP 주소 또는 호스트 이름으로 바꿉니다.
   ![config gateway의 스크린샷](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. 다음 명령을 실행하여 Intel NUC에 도우미 도구를 설치합니다.

   ```bash
   gulp install-tools
   ```

5. 다음 그림과 같이 전원 단추를 눌러 SensorTag를 켜면 녹색 LED가 깜박입니다.

   ![센서 태그 켜기](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. 다음 명령을 실행하여 SensorTag 장치를 검색합니다.

   ```bash
   gulp discover-sensortag
   ```

7. 다음 명령을 실행하여 SensorTag 및 Intel NUC 간의 연결을 테스트합니다.

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   `{mac address}`를 이전 단계에서 가져온 MAC 주소로 바꿉니다.

## <a name="get-the-connection-string-of-sensortag"></a>SensorTag의 연결 문자열 가져오기

SensorTag의 Azure IoT Hub 연결 문자열을 가져오려면 호스트 컴퓨터에서 다음 명령을 실행합니다.

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}`은 사용한 IoT Hub 이름입니다. 단원 2에서 값을 변경하지 않았다면 iot-gateway를 `{resource group name}` 값으로 사용하고 mydevice를 `{device id}` 값으로 사용합니다.

## <a name="configure-the-ble-sample-application"></a>BLE 샘플 응용 프로그램 구성

BLE 샘플 응용 프로그램을 구성하고 실행하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 Visual Studio Code에서 `config-sensortag.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![config sensortag의 스크린샷](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. 코드에서 다음 내용을 바꿉니다.
   - `[IoT hub name]`을 사용한 IoT Hub 이름으로 바꿉니다.
   - `[IoT device connection string]`을 가져온 SensorTag 연결 문자열로 바꿉니다.
   - `[device_mac_address]`를 가져온 SensorTag의 MAC 주소로 바꿉니다.

3. BLE 샘플 응용 프로그램을 실행합니다.

   BLE 샘플 응용 프로그램을 실행하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

   1. SensorTag를 켭니다.

   2. 다음 명령을 실행하여 Intel NUC에 BLE 샘플 응용 프로그램을 배포하고 실행합니다.
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>BLE 샘플 응용 프로그램 작동 확인

이제 다음과 유사한 출력이 표시됩니다.

![BLE 샘플 응용 프로그램 출력](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

샘플 응용 프로그램은 계속 온도 데이터를 수집하여 IoT Hub로 보냅니다. 샘플 응용 프로그램은 보내고 40초 후에 자동으로 종료됩니다.

## <a name="summary"></a>요약

SensorTag와 Intel NUC 간의 연결을 성공적으로 설정하고 SensorTag에서 데이터를 수집하여 IoT Hub로 보내는 BLE 샘플 응용 프로그램을 실행합니다. 이제 IoT Hub가 데이터를 수신했는지 확인하는 방법을 배울 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub에서 메시지 읽기](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
