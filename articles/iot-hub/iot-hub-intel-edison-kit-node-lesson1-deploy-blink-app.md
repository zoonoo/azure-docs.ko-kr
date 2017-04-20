---
title: "Azure IoT에 Intel Edison(노드) 연결 - 단원 1: 앱 배포 | Microsoft Docs"
description: "GitHub에서 샘플 C 응용 프로그램을 복제하고 gulp를 실행하여 이 응용 프로그램을 Intel Edison 보드에 배포합니다. 이 샘플 응용 프로그램은 보드에 연결된 LED를&2;초마다 깜박이게 합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 프로젝트, arduino led 깜박임, arduino led 깜박임 코드, arduino 깜박임 프로그램, arduino 깜박임 예제"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c
ms.lasthandoff: 01/25/2017


---
# <a name="create-and-deploy-the-blink-application"></a>깜박임 응용 프로그램 만들기 및 배포
## <a name="what-you-will-do"></a>수행할 사항
GitHub에서 샘플 C 응용 프로그램을 복제하고 gulp 도구를 사용하여 Intel Edison에 샘플 응용 프로그램을 배포합니다. 샘플 응용 프로그램은 보드에 연결된 LED를&2;초마다 깜박이게 합니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
* Edison에서 샘플 응용 프로그램을 배포하고 실행하는 방법.

## <a name="what-you-need"></a>필요한 항목
다음 작업을 성공적으로 완료해야 합니다.

* [장치 구성][configure-your-device]
* [도구 얻기][get-the-tools]

## <a name="open-the-sample-application"></a>샘플 응용 프로그램 열기
샘플 응용 프로그램을 열려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 GitHub에서 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Repo 구조][repo-structure]

`app` 하위 폴더의 파일은 LED 제어 코드가 있는 핵심 원본 파일입니다.

### <a name="install-application-dependencies"></a>응용 프로그램 종속성 설치
다음 명령을 실행하여 샘플 응용 프로그램에 필요한 라이브러리 및 기타 모듈을 설치합니다.

```bash
npm install
```

## <a name="configure-the-device-connection"></a>장치 연결 구성
장치 연결을 구성하려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 장치 구성 파일을 생성합니다.

   ```bash
   gulp init
   ```

   구성 파일 `config-edison.json`에는 Edison에 로그인하는 데 사용하는 사용자 자격 증명이 포함됩니다. 사용자 자격 증명의 유출을 방지하기 위해 구성 파일은 컴퓨터 홈 폴더의 `.iot-hub-getting-started` 하위 폴더에 생성됩니다.

2. 다음 명령을 실행하여 Visual Studio Code에서 장치 구성 파일을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. 자리 표시자 `[device hostname or IP address]` 및 `[device password]`를 이전 단원에서 적어 둔 IP 주소 및 암호로 바꿉니다.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

축하합니다. Edison의 첫 번째 샘플 응용 프로그램을 만들었습니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행

### <a name="deploy-and-run-the-sample-app"></a>샘플 앱 배포 및 실행
다음 명령을 실행하여 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>앱 작동 확인
샘플 응용 프로그램은 LED를 20번 깜박인 후 자동으로 종료됩니다. LED가 깜박이지 않으면 [문제 해결 가이드][troubleshooting]에서 일반적인 문제에 대한 솔루션을 참조하세요.

![LED 깜박임][led-blinking]

## <a name="summary"></a>요약
Edison 작동에 필요한 도구를 설치했으며 LED를 깜박이게 하는 샘플 응용 프로그램을 Edison에 배포했습니다. 이제 메시지 송수신을 위해 Azure IoT Hub에 Edison을 연결하는 다른 샘플 응용 프로그램을 만들고, 배포하고, 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 도구 얻기][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md

