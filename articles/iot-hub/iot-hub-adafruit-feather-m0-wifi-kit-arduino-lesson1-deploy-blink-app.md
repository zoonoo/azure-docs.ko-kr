---
title: "Azure IoT에 Arduino 연결 - 단원 1: 앱 배포 | Microsoft Docs"
description: "GitHub에서 샘플 Arduino 응용 프로그램을 복제하고 gulp를 실행하여 이 응용 프로그램을 Adafruit Feather M0 WiFi에 배포합니다. 이 샘플 응용 프로그램은 GPIO를 깜박입니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 프로젝트, arduino led 깜박임, arduino led 깜박임 코드, arduino 깜박임 프로그램, arduino 깜박임 예제"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>깜박임 응용 프로그램 만들기 및 배포
## <a name="what-you-will-do"></a>수행할 사항
GitHub에서 샘플 Arduino 응용 프로그램을 복제하고 gulp 도구를 사용하여 샘플 응용 프로그램을 Adafruit Feather M0 WiFi Arduino 보드에 배포합니다. 샘플 응용 프로그램은 GPIO #13 온보드 LED를 2초마다 깜박이게 합니다.

문제가 있으면 [문제 해결 페이지][troubleshooting-page]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
* Arduino 보드에서 샘플 응용 프로그램 배포 및 실행 방법

## <a name="what-you-need"></a>필요한 항목
다음 작업을 성공적으로 완료해야 합니다.

* [장치 구성][configure-your-device]
* [도구 얻기][get-the-tools]

## <a name="open-the-sample-application"></a>샘플 응용 프로그램 열기
샘플 응용 프로그램을 열려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 GitHub에서 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![Repo 구조][repo-structure]

`app` 하위 폴더의 `app.ino` 파일은 LED 제어 코드가 담긴 핵심 원본 파일입니다.

### <a name="install-application-dependencies"></a>응용 프로그램 종속성 설치
다음 명령을 실행하여 샘플 응용 프로그램에 필요한 라이브러리 및 기타 모듈을 설치합니다.

```bash
npm install
```

## <a name="configure-the-device-connection"></a>장치 연결 구성
장치 연결을 구성하려면 다음 단계를 따릅니다.

1. 장치 검색 cli를 사용하여 장치의 직렬 포트를 가져옵니다.

   ```bash
   devdisco list --usb
   ```

   다음과 유사한 출력이 표시되면 Arduino 보드에 대한 usb COM 포트를 찾아야 합니다. ![장치 검색][device-discovery]

2. 단원 폴더에서 `config.json` 파일을 열고 검색한 COM 포트 번호의 값을 추가합니다.

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Windows 플랫폼에서 COM 포트는 `COM1, COM2, ...` 형식입니다. macOS 또는 Ubuntu에서는 `/dev/`로 시작합니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
### <a name="install-the-required-tools-for-your-arduino-board"></a>Arduino 보드용 필수 도구 설치

다음 명령을 실행하여 Arduino 보드용 Azure IoT Hub SDK를 설치합니다.

```bash
gulp install-tools
```

네트워크 연결에 따라 이 작업을 완료하는 데 시간이 오래 걸릴 수 있습니다.

> [!NOTE]
> gulp 작업(`install-tools`, `run`)을 실행할 때는 실행 중인 Arduino IDE 인스턴스를 종료하세요.

### <a name="deploy-and-run-the-sample-app"></a>샘플 앱 배포 및 실행
다음 명령을 실행하여 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>앱 작동 확인
LED가 깜박이지 않으면 [문제 해결 가이드][troubleshooting-page]에서 일반적인 문제에 대한 솔루션을 참조하세요.

![LED 깜박임][led-blinking]

## <a name="summary"></a>요약
Arduino 보드 작업에 필요한 도구를 설치했으며 LED를 깜박이게 하는 샘플 응용 프로그램을 Arduino 보드에 배포했습니다. 이제 메시지 송수신을 위해 Azure IoT Hub에 Arduino 보드를 연결하는 다른 샘플 응용 프로그램을 만들고, 배포하고, 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 도구 얻기][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
