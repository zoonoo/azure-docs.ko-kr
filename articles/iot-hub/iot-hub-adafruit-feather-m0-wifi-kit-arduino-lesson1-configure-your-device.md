---
title: "Azure IoT 시작 키트 구성 | Microsoft Docs"
description: "처음 사용 시 Adafruit Feather M0 WiFi를 구성합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 설정, pc에 arduino 연결, arduino 설치, arduino 보드"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: fd3f55ee79bdd4d485ae3a479a5a072905956bc9
ms.openlocfilehash: 47d7a4e68574c5f88b336e9ca7b21dbba24a94cf


---
# <a name="configure-your-device"></a>장치 구성
## <a name="what-you-will-do"></a>수행할 사항
보드를 조립하여 전원을 켜서 Adafruit Feather M0 WiFi Arduino 보드를 처음 사용하도록 구성합니다. 문제가 있으면 [문제 해결 페이지](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-need"></a>필요한 항목
이 작업을 완료하려면 Adafruit Feather M0 WiFi 시작 키트에서 다음 부품이 필요합니다.

* Adafruit Feather M0 WiFi 보드
* Micro B-A형 USB 케이블

![키트][kit]

다음 항목도 필요합니다.

* Windows, Mac 또는 Linux를 실행하는 컴퓨터 
* 무선으로 Arduino 보드 연결
* 구성 도구를 다운로드하기 위한 인터넷 연결

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.

* 다음 단원을 위해 Arduino 보드를 조립하고 전원을 켜는 방법
* Ubuntu에서 직렬 포트 권한을 추가하는 방법

## <a name="connect-your-arduino-board-to-your-computer"></a>컴퓨터에 Arduino 보드 연결

1. 마이크로 USB 케이블을 위쪽 마이크로 USB 포트에 꽂습니다.

   ![위쪽 마이크로 USB 포트][top-micro-usb-port]

2. USB 케이블의 다른 쪽을 컴퓨터에 연결합니다.

   ![컴퓨터 USB][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Ubuntu에서 직렬 포트 권한 추가

Windows 또는 macOS를 사용하는 경우 이 섹션을 건너뛸 수 있습니다. Ubuntu의 경우 일반적인 Linux 사용자가 Arduino 보드의 USB 포트에서 작동할 수 있는 권한을 가지고 있는지 확인하려면 다음 단계가 필요합니다.

1. 이제 터미널에서 일반 사용자로 다음을 수행합니다.

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   다음과 비슷한 결과가 표시됩니다.

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   "0" 대신 다른 숫자가 될 수도 있고 여러 항목이 반환될 수도 있습니다. 첫 번째 경우 필요한 데이터는 `uucp`이고 두 번째의 경우 `dialout`인데 이는 파일의 그룹 소유자입니다.

2. 그룹에 사용자를 추가합니다.

   ```bash
   sudo usermod -a -G group-name username
   ```

   여기서 `group-name`은 첫 번째 단계에 있는 데이터이고 `username`은 Linux 사용자 이름입니다.

3. 이 변경 내용을 적용하고 설치를 완료하려면 로그아웃한 후 다시 로그인해야 합니다.

## <a name="summary"></a>요약
이 문서에서는 Arduino 보드를 구성하는 방법을 알아보았습니다. 다음 작업은 Arduino 보드에서 샘플 응용 프로그램 실행을 위한 준비로 필요한 도구 및 소프트웨어를 설치하는 것입니다.

![하드웨어는 준비되었습니다.][hardware-is-ready]

## <a name="next-steps"></a>다음 단계
[도구 얻기][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md


<!--HONumber=Dec16_HO2-->


