---
title: "Azure IoT에 Intel Edison(노드) 연결 - 단원 4: 메시지 수신 | Microsoft Docs"
description: "샘플 응용 프로그램은 Edison에서 실행되며 IoT Hub에서 들어오는 메시지를 모니터링합니다. 새로운 gulp 작업은 IoT Hub에서 Edison으로 메시지를 보내고 LED를 깜빡입니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "웹에서 arduino led 제어, 웹을 통한 arduino led 제어"
ms.assetid: bc738bf6-e38d-4024-82d7-39b6c2d4bacb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 4dfddf6a2664abbdfd9b5d782dafc9e5ff243e5a


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기
이 문서에서는 샘플 응용 프로그램을 Intel Edison에 배포합니다. 샘플 응용 프로그램은 IoT Hub에서 들어오는 메시지를 모니터링합니다. 컴퓨터에서 gulp 작업을 실행하여 IoT Hub에서 Edison으로 메시지를 보내기도 합니다. 샘플 응용 프로그램이 메시지를 수신하면 LED를 깜박입니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-do"></a>수행할 사항
* 샘플 응용 프로그램을 IoT Hub에 연결합니다.
* 샘플 응용 프로그램을 배포 및 실행합니다.
* IoT Hub에서 Edison으로 메시지를 보내고 LED를 깜빡입니다.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* IoT Hub에서 들어오는 메시지를 모니터링하는 방법.
* IoT Hub에서 Edison으로 클라우드-장치 메시지를 보내는 방법.

## <a name="what-you-need"></a>필요한 항목
* Intel Edison, 사용하도록 설정. Edison을 설정하는 방법은 [장치 구성][configure-your-device]을 참조하세요.
* Azure 구독에서 만든 IoT Hub. IoT Hub를 만드는 방법을 알아보려면 [Azure IoT Hub 만들기][create-your-azure-iot-hub]를 참조하세요.

## <a name="connect-the-sample-application-to-your-iot-hub"></a>샘플 응용 프로그램을 IoT Hub에 연결
1. repo 폴더 `iot-hub-node-edison-getting-started`에 있어야 합니다. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.

   ```bash
   cd Lesson4
   code .
   ```

   `app` 하위 폴더의 파일은 IoT Hub에서 들어오는 메시지를 모니터링할 코드를 포함하는 핵심 원본 파일입니다. `blinkLED` 함수는 LED를 깜빡입니다.

   ![샘플 응용 프로그램의 Repo 구조][repo-structure]
2. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   npm install
   gulp init
   ```

   [Azure 함수 앱 및 저장소 계정 만들기][create-an-azure-function-app-and-storage-account] 단계를 이 컴퓨터에서 완료했다면 모든 구성이 상속되므로, 샘플 응용 프로그램 배포 및 실행 작업으로 단계를 건너뛸 수 있습니다. 다른 컴퓨터에서 [Azure 함수 앱 및 저장소 계정 만들기][create-an-azure-function-app-and-storage-account] 단계를 완료했다면 `config-edison.json` 파일에서 자리 표시자를 바꿔야 합니다. `config-edison.json` 파일은 홈 폴더의 하위 폴더에 있습니다.

   ![config-edison.json 파일의 내용](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * **[device hostname or IP address]**를 장치를 구성할 때 적어 둔 장치 IP 주소로 바꿉니다.
   * **[IoT device connection string]**을 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}` 명령을 실행하여 가져온 장치 연결 문자열로 바꿉니다.
   * **[IoT hub connection string]**을 `az iot hub show-connection-string --name {my hub name}` 명령을 실행하여 가져온 IoT Hub 연결 문자열로 바꿉니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Edison에서 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

이 gulp 명령은 샘플 응용 프로그램을 Edison에 배포합니다. 그런 다음 Edison에서 응용 프로그램을 실행하고 호스트 컴퓨터에서 별도의 작업을 실행하여 20개의 blink 메시지를 IoT Hub에서 Edison에 보냅니다.

샘플 응용 프로그램이 실행된 후 IoT Hub의 메시지를 수신 대기하기 시작합니다. 그 동안 gulp 작업은 IoT Hub에서 Edison에 여러 개의 "blink" 메시지를 보냅니다. Edison에서 blink 메시지를 수신할 때마다 샘플 응용 프로그램이 `blinkLED` 함수를 호출하여 LED를 깜빡입니다.

gulp 작업이 IoT Hub에서 Edison에 메시지를 20개 보내기 때문에 2초마다 LED가 깜박이는 것을 볼 수 있습니다. 마지막 깜빡임은 응용 프로그램 실행을 중지하는 "stop" 메시지입니다.

![gulp 명령 및 blink 메시지가 있는 샘플 응용 프로그램][gulp-command-and-blink-messages]

## <a name="summary"></a>요약
IoT Hub에서 Edison에 메시지를 보내서 LED를 깜빡이는 데 성공했습니다. 다음 작업인 LED 켜기 및 끄기 동작 변경은 옵션입니다.

## <a name="next-steps"></a>다음 단계
[LED 켜기 및 끄기 동작 변경][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md


<!--HONumber=Jan17_HO4-->


