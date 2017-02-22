---
title: "Azure IoT에 Intel Edison(노드) 연결 - 단원 1: 장치 구성 | Microsoft Docs"
description: "처음 사용을 위한 Intel Edison을 구성합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 설정, PC에 arduino 연결, arduino 설정, arduino 보드"
ms.assetid: 372c9b6d-e701-4ff6-8151-d262aa76aa55
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 203d3976b9ba0960f5a3dbff4e2b1fcdf4c0dc33


---
# <a name="configure-your-intel-edison"></a>Intel Edison 구성
## <a name="what-you-will-do"></a>수행할 사항
보드를 조립하고, 전원을 켜고, 데스크톱 OS에 구성 도구를 설치하여 Edison 펌웨어를 플래시하고, 해당 암호를 설정한 후 Wi-Fi에 연결하여 처음 사용할 수 있게 Intel Edison을 구성합니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.

* Edison 보드를 조립하고 전원을 켜는 방법
* Edison 펌웨어를 플래시하고, 암호를 설정하고, Wi-Fi를 연결하는 방법

## <a name="what-you-need"></a>필요한 항목
이 작업을 완료하려면 Intel Edison 시작 키트에서 다음 부품이 필요합니다.

* Intel® Edison 모듈
* Arduino 확장 보드
* 포장에 포함된 스페이서 바 또는 나사(모듈을 확장 보드에 고정하기 위한&2;개의 나사와&4;개의 나사 세트 및 플라스틱 스페이서 포함)
* Micro B-A형 USB 케이블
* DC(직류) 전원 공급 장치 전원 공급 장치 정격은 다음과 같아야 합니다.
  - 7-15V DC
  - 1500mA 이상
  - 중앙/내부 핀은 전원 공급 장치의 양극이어야 합니다.

  ![시작 키트의 항목](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

다음 항목도 필요합니다.

* Windows, Mac 또는 Linux를 실행하는 컴퓨터 
* Edison을 연결할 무선 연결
* 구성 도구를 다운로드하기 위한 인터넷 연결

## <a name="assemble-your-board"></a>보드 조립

이 섹션에는 Intel® Edison 모듈을 확장 보드를 연결하는 단계가 포함됩니다.

1. 확장 보드의 흰색 윤곽 안에 Intel® Edison 모듈을 놓고 모듈의 구멍과 확장 보드의 나사를 맞춥니다.

2. 딸깍 소리가 느껴질 때까지 `What will you make?` 아래에서 모듈을 누릅니다.

   ![보드 2 조립](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. 2개의 육각 너트(포장에 포함)를 사용하여 모듈을 확장 보드에 고정합니다.

   ![보드 3 조립](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. 확장 보드의 구석에 있는&4;개 구멍 중 하나에 나사를 끼웁니다. 흰색 플라스틱 스페너 중 하나를 돌려 나사를 조입니다.

   ![보드 4 조립](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. 나머지&3;개 구석의 스페이서에 대해 같은 작업을 반복합니다.

   ![보드 5 조립](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

이제 보드가 조립됩니다.

   ![조립된 보드](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

## <a name="power-up-edison"></a>Edison 전원 켜기

1. 전원 공급 장치를 꽂습니다.

   ![전원 공급 장치 꽂기](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. 녹색 LED(Arduino* 확장 보드에 DS1으로 레이블 지정)가 켜진 후 계속 켜져 있습니다.

3. 보드가 부팅을 마칠 때까지&1;분 정도 대기합니다.

   > [!NOTE]
   > DC 전원 공급 장치가 없으면 USB 포트를 통해 여전히 보드의 전원을 켤 수 있습니다. 자세한 내용은 `Connect Edison to your computer` 섹션을 참조하세요. 이러한 방식으로 보드를 켜면 보드에서 예기치 않은 동작이 나타날 수 있습니다. 이러한 현상은 Wi-Fi 또는 구동 모터를 사용할 때 더욱 두드러집니다.

## <a name="connect-edison-to-your-computer"></a>Edison을 컴퓨터에 연결

1. Edison이 장치 모드가 되도록&2;개의 마이크로 USB 포트 쪽으로 마이크로 스위치를 내립니다. 장치 모드와 호스트 모드 간 차이점에 대해서는 [여기](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)를 참조하세요.

   ![마이크로 스위치 내리기](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. 마이크로 USB 포트를 위쪽 마이크로 USB 케이블에 꽂습니다.

   ![위쪽 마이크로 USB 포트](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. USB 케이블의 다른 쪽을 컴퓨터에 연결합니다.

   ![컴퓨터 USB](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. 컴퓨터에 새 장치를 탑재(컴퓨터에 SD 카드를 꽂는 것과 매우 유사함)할 때 보드가 완전히 초기화된다는 것을 알 수 있습니다.

## <a name="download-and-run-the-configuration-tool"></a>구성 도구 다운로드 및 실행
`Installers` 제목 아래 나열된 [이 링크](https://software.intel.com/en-us/iot/hardware/edison/downloads)에서 최신 구성 도구를 가져옵니다. 도구를 실행하고 화면 지시를 따른 다음 필요할 때 다음을 클릭합니다.

### <a name="flash-firmware"></a>펌웨어 플래시
1. `Set up options` 페이지에서 `Flash Firmware`를 클릭합니다.
2. 다음 중 하나를 수행하여 보드에서 플래시할 이미지를 선택합니다.
   - Intel에서 사용 가능한 최신 펌웨어 이미지를 다운로드한 후 이 이미지로 보드를 플래시하려면 `Download the latest image version xxxx`를 선택합니다.
   - 컴퓨터에 이미 저장한 이미지로 보드를 플래시하려면 `Select the local image`를 선택합니다. 보드에 플래시할 이미지를 찾아 선택합니다.
3. 설치 도구는 보드를 플래시하려고 합니다. 전체 플래시 프로세스는 10분 정도 걸릴 수 있습니다.

### <a name="set-password"></a>암호 설정
1. `Set up options` 페이지에서 `Enable Security`를 클릭합니다.
2. Intel® Edison 보드에 대한 사용자 지정 이름을 설정할 수 있습니다. 선택 사항입니다.
3. 보드에 대한 암호를 입력한 후 `Set password`를 클릭합니다.
4. 나중에 사용되는 암호에 표시합니다.

### <a name="connect-wi-fi"></a>Wi-Fi 연결
1. `Set up options` 페이지에서 `Connect Wi-Fi`를 클릭합니다. 컴퓨터가 사용할 수 있는 Wi-Fi 네트워크를 검색하므로 최대&1;분 정도 대기합니다.
2. `Detected Networks` 드롭다운 목록에서 네트워크를 선택합니다.
3. `Security` 드롭다운 목록에서 네트워크의 보안 유형을 선택합니다.
4. 로그인 및 암호 정보를 입력한 다음 `Configure Wi-Fi`를 클릭합니다.
5. 나중에 사용되는 IP 주소에 표시합니다.

> [!NOTE]
> Edison이 컴퓨터와 같은 네트워크에 연결되어 있는지 확인합니다. 해당 IP 주소를 사용하여 컴퓨터가 Edison에 연결됩니다.

축하합니다. Edison 구성을 마쳤습니다.

## <a name="summary"></a>요약
이 문서에서는 구성 도구를 사용하여 Edison 보드를 조립하고, 펌웨어를 플래시하고, 암호를 설정하고, Wi-Fi에 연결하는 방법을 알아보았습니다. 아직 LED에는 불이 들어오지 않습니다. 다음 작업은 Edision에서 샘플 응용 프로그램 실행을 위한 준비로 필요한 도구 및 소프트웨어를 설치하는 것입니다.

## <a name="next-steps"></a>다음 단계
[도구 얻기][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md


<!--HONumber=Jan17_HO4-->


