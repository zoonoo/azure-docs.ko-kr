---
title: "Azure IoT에 Arduino(C) 연결 - 단원 3: 샘플 실행 | Microsoft Docs"
description: "IoT Hub에 메시지를 보내고 LED를 깜빡이는 샘플 응용 프로그램을 Adafruit Feather M0 WiFi에 배포하고 실행합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 클라우드 서비스, arduino 클라우드에 데이터 보내기"
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기
## <a name="what-you-will-do"></a>수행할 사항
이 문서는 IoT Hub에 메시지를 보내는 샘플 응용 프로그램을 Adafruit Feather M0 WiFi Arduino 보드에 배포하고 실행하는 방법을 보여 줍니다.

문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
Gulp 도구를 사용하여 Arduino 보드에서 샘플 Arduino 응용 프로그램을 배포하고 실행하는 방법을 알아봅니다.

## <a name="what-you-need"></a>필요한 항목
* 이 작업을 시작하기 전에 [IoT Hub 메시지를 처리하고 저장하기 위해 Azure 함수 앱 및 저장소 계정 만들기][process-and-store-iot-hub-messages]를 완료해야 합니다.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT Hub 및 장치 연결 문자열 가져오기
장치 연결 문자열은 Arduino 보드를 IoT Hub에 연결하는 데 사용됩니다. IoT Hub 연결 문자열은 IoT Hub를 장치 ID에 연결하는 데 사용됩니다. 이 ID는 IoT Hub에서 Arduino 보드를 나타냅니다.

* 다음 Azure CLI 명령을 실행하여 리소스 그룹의 모든 IoT Hub를 나열합니다.

```bash
az iot hub list -g iot-sample --query [].name
```

값을 변경하지 않았다면 `iot-sample`을 `{resource group name}` 값으로 사용합니다.

* 다음 Azure CLI 명령을 실행하여 IoT Hub 연결 문자열을 가져옵니다.

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}`은 IoT Hub를 만들고 Arduino 보드를 등록할 때 지정한 이름입니다.

* 다음 명령을 실행하여 장치 연결 문자열을 가져옵니다.

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

값을 변경하지 않았다면 `mym0wifi`을 `{device id}` 값으로 사용합니다.
## <a name="configure-the-device-connection"></a>장치 연결 구성
장치 연결을 구성하려면 다음 단계를 따릅니다.

1. 장치 검색 cli를 사용하여 장치의 직렬 포트를 가져옵니다.

   ```bash
   devdisco list --usb
   ```

   다음과 유사한 출력이 표시되면 Arduino 보드에 대한 usb COM 포트를 찾아야 합니다.

   ![장치 검색][device-discovery]

2. 단원 폴더에서 `config.json` 파일을 열고 검색한 COM 포트 번호의 값을 추가합니다.

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Windows 플랫폼에서 COM 포트는 `COM1, COM2, ...` 형식입니다. macOS 또는 Ubuntu에서는 `/dev/`로 시작합니다.

3. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. 다음 명령을 실행하여 Visual Studio Code에서 장치 구성 파일 `config-arduino.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. `config-arduino.json` 파일에서 다음 내용을 바꿉니다.

   * **[Wi-Fi SSID]**를 인터넷에 연결된 Wi-Fi SSID로 바꿉니다.
   * **[Wi-Fi password]**를 사용자의 Wi-Fi 암호로 바꿉니다. Wi-Fi에 암호가 필요하지 않은 경우 문자열을 제거합니다.
   * **[IoT device connection string]**을 가져온 `device connection string`으로 바꿉니다.
   * **[IoT hub connection string]**을 가져온 `iot hub connection string`으로 바꿉니다.

   > [!NOTE]
   > 이 문서에서는 `azure_storage_connection_string`이 필요하지 않습니다. 그대로 유지합니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Arduino 보드에서 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> 기본 gulp 작업은 `install-tools` 및 `run` 작업을 순차적으로 실행합니다. [깜박임 앱 배포][deployed-the-blink-app] 시 별도로 이러한 작업을 실행했습니다.

## <a name="verify-that-the-sample-application-works"></a>샘플 응용 프로그램 작동 확인
GPIO #0 온보드 LED가 2초마다 깜박여야 합니다. LED가 깜빡일 때마다 샘플 응용 프로그램은 IoT Hub에 메시지를 전송하고 해당 메시지가 IoT Hub에 성공적으로 전송되었는지 확인합니다. 또한 IoT Hub가 수신한 각 메시지가 콘솔 창에 출력됩니다. 샘플 응용 프로그램은 메시지를 20개 보낸 후에 자동으로 종료됩니다.

![보낸 메시지와 수신한 메시지가 있는 샘플 응용 프로그램][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>요약
IoT Hub에 장치-클라우드 메시지를 보내기 위해 Arduino 보드에 깜빡이는 샘플 응용 프로그램을 새로 배포하고 실행했습니다. 이제 저장소 계정에 메시지가 작성될 때 해당 메시지를 모니터링합니다.

## <a name="next-steps"></a>다음 단계
[Azure Storage에 유지되는 메시지 읽기][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md


<!--HONumber=Jan17_HO4-->


