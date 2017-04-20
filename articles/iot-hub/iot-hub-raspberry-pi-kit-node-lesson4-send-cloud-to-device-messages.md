---
featureFlags:
- usabilla
title: "Azure IoT에 Raspberry Pi(노드) 연결 - 단원 4: 클라우드-장치 | Microsoft Docs"
description: "샘플 응용 프로그램은 Pi에서 실행되며 IoT Hub에서 들어오는 메시지를 모니터링합니다. 새로운 gulp 작업은 IoT Hub에서 Pi로 메시지를 보내고 LED를 깜빡입니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "클라우드-장치, 클라우드의 메시지"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df
ms.lasthandoff: 01/24/2017


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기
이 문서에서는 샘플 응용 프로그램을 Raspberry Pi 3에 배포합니다. 샘플 응용 프로그램은 IoT Hub에서 들어오는 메시지를 모니터링합니다. 컴퓨터에서 gulp 작업을 실행하여 IoT Hub에서 Pi로 메시지를 보내기도 합니다. 샘플 응용 프로그램이 메시지를 수신하면 LED를 깜박입니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-do"></a>수행할 사항
* 샘플 응용 프로그램을 IoT Hub에 연결합니다.
* 샘플 응용 프로그램을 배포 및 실행합니다.
* IoT Hub에서 Pi로 메시지를 보내고 LED를 깜빡입니다.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* IoT Hub에서 들어오는 메시지를 모니터링하는 방법.
* IoT Hub에서 Pi로 클라우드-장치 메시지를 보내는 방법.

## <a name="what-you-need"></a>필요한 항목
* 사용하도록 설정된 Raspberry Pi 3. Pi를 설정하는 방법은 [장치 구성](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)을 참조하세요.
* Azure 구독에서 만든 IoT Hub. IoT Hub를 만드는 방법을 알아보려면 [IoT Hub 만들기 및 Raspberry Pi 3 등록](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)을 참조하세요.

## <a name="connect-the-sample-application-to-your-iot-hub"></a>샘플 응용 프로그램을 IoT Hub에 연결
1. repo 폴더 `iot-hub-node-raspberrypi-getting-started`에 있어야 합니다. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.
   
   ```bash
   cd Lesson4
   code .
   ```
   
   `app` 하위 폴더에서 `app.js` 파일에 주목합니다. `app.js` 파일은 IoT Hub에서 들어오는 메시지를 모니터링할 코드를 포함하는 주요 소스 파일입니다. `blinkLED` 함수는 LED를 깜빡입니다.
   
   ![샘플 응용 프로그램의 Repo 구조](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. 다음 명령을 사용하여 구성 파일을 초기화합니다.
   
   ```bash
   npm install
   gulp init
   ```
   
   [Azure 함수 앱 및 저장소 계정 만들기](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) 단계를 이 컴퓨터에서 완료했다면 모든 구성이 상속되므로, 샘플 응용 프로그램 배포 및 실행 작업으로 건너뛸 수 있습니다. [Azure 함수 앱 및 저장소 계정 만들기](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) 단계를 다른 컴퓨터에서 완료했다면 `config-raspberrypi.json` 파일에서 자리 표시자를 바꿔야 합니다. `config-raspberrypi.json` 파일은 홈 폴더의 하위 폴더에 있습니다.
   
   ![config-raspberrypi.json 파일의 내용](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* **[device hostname or IP address]**를 Pi의 IP 주소 또는 `devdisco list --eth` 명령을 실행하여 가져온 호스트 이름으로 바꿉니다.
* **[IoT device connection string]**을 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}` 명령을 실행하여 가져온 장치 연결 문자열로 바꿉니다.
* **[IoT hub connection string]**을 `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}` 명령을 실행하여 가져온 IoT Hub 연결 문자열로 바꿉니다.

> [!NOTE]
> 단원 1에서 **gulp install-tools**를 실행하지 않았다면 이 명령도 실행합니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Pi에 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

이 명령은 샘플 응용 프로그램을 Pi에 배포합니다. 그런 다음 Pi에서 응용 프로그램을 실행하고 호스트에서 별도의 작업을 실행하여 20개의 blink 메시지를 IoT Hub에서 Pi에 보냅니다.

샘플 응용 프로그램이 실행된 후 IoT Hub의 메시지를 수신 대기하기 시작합니다. 그 동안 gulp 작업은 IoT Hub에서 Pi에 여러 개의 "blink" 메시지를 보냅니다. Pi에서 blink 메시지를 수신할 때마다 샘플 응용 프로그램이 `blinkLED` 함수를 호출하여 LED를 깜빡입니다.

gulp 작업이 IoT Hub에서 Pi에 메시지를 20개 보내기 때문에 2초마다 LED가 깜박이는 것을 볼 수 있습니다. 마지막 깜빡임은 응용 프로그램에게 실행을 중지하도록 알려주는 "stop" 메시지입니다.

![gulp 명령 및 blink 메시지가 있는 샘플 응용 프로그램](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>요약
IoT Hub에서 Pi에 메시지를 보내서 LED를 깜빡이는 데 성공했습니다. 다음 작업인 LED 켜기 및 끄기 동작 변경은 옵션입니다.

## <a name="next-steps"></a>다음 단계
[LED 켜기 및 끄기 동작 변경](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


