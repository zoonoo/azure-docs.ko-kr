---
title: "Azure IoT에 Raspberry Pi(노드) 연결 - 단원 1: 장치 구성 | Microsoft Docs"
description: Configure Raspberry Pi 3 for first-time use and install the Raspbian OS, a free operating system that is optimized for the Raspberry Pi hardware.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspbian 설치, Raspbian 다운로드, Raspbian 설치 방법, Raspbian 설정, Raspberry Pi 설치 Raspbian, Raspberry Pi 설치 OS, Raspberry Pi SD 카드 설치, Raspberry Pi 연결, Raspberry Pi에 연결, Raspberry Pi 연결"
ms.assetid: 43f7c2cf-f1a5-4dd5-93f0-7e546c6dc91e
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cc61bd7ab1f81ccdc5e9c73c1861d29dafa59c12


---
# <a name="configure-your-device"></a>장치 구성
## <a name="what-you-will-do"></a>수행할 사항
최초 사용을 위해 Pi를 구성하고 Raspbian 운영 체제를 설치합니다. Raspbian은 Raspberry Pi 하드웨어에 최적화된 무료 운영 체제입니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색할 수 있습니다.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.

* Pi에 Raspbian을 설치하는 방법.
* USB 케이블을 사용하여 Pi에 전원을 공급하는 방법.
* 이더넷 케이블이나 무선 네트워크를 사용하여 Pi를 네트워크에 연결하는 방법.
* LED를 실험용 회로판에 추가하고 Pi에 연결하는 방법.

## <a name="what-you-will-need"></a>필요한 사항
이 작업을 완료하려면 Raspberry Pi 3 시작 키트에서 다음 부품이 필요합니다.

* Raspberry Pi 3 보드
* 16GB microSD 카드
* 6피트 마이크로 USB 케이블과 5볼트 2암페어 전원 공급 장치
* 실험용 회로판
* 커넥터 와이어
* 560옴 저항기
* 확산형 10mm LED
* 이더넷 케이블 

![시작 키트의 항목](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

다음 항목도 필요합니다.

* Pi를 연결할 유선 또는 무선 연결.
* 운영 체제 이미지를 microSD 카드에 굽기 위한 USB-SD 어댑터 또는 miniSD 카드.
* Windows, Mac 또는 Linux를 실행하는 컴퓨터  컴퓨터는 microSD 카드에 Raspbian을 설치하는 데 사용됩니다.
* 필요한 도구 및 소프트웨어를 다운로드하기 위한 인터넷 연결.

## <a name="install-raspbian-on-the-microsd-card"></a>microSD 카드에 Raspbian 설치
Raspbian 이미지를 설치를 위해 microSD 카드를 준비합니다.

1. Raspbian을 다운로드합니다.
   1. Raspbian Jessie with Pixel의 zip 파일을 [다운로드](https://www.raspberrypi.org/downloads/raspbian/)합니다.
   2. 컴퓨터의 폴더에 Raspbian 이미지의 압축을 풉니다.
2. microSD 카드에 Raspbian을 설치합니다.
   1. Etcher SD 카드 버너 유틸리티를 [다운로드](https://www.etcher.io)하여 설치합니다.
   2. Etcher를 실행하고 1단계에서 압축을 푼 Raspbian 이미지를 선택합니다.
   3. microSD 카드 드라이브를 선택합니다.
      Etcher가 이미 올바른 드라이브를 선택했을 수 있습니다.
   4. **Flash**를 클릭하여 microSD 카드에 Raspbian을 설치합니다.
   5. 설치가 완료되면 컴퓨터에서 microSD 카드를 제거합니다.
      완료되면 Etcher가 microSD 카드를 자동으로 배출하거나 탑재를 해제하므로 microSD 카드를 바로 제거하는 것이 안전합니다.
   6. Pi에 microSD 카드를 삽입합니다.

![SD 카드 삽입](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>Pi 켜기
마이크로 USB 케이블 및 전원 공급 장치를 사용하여 Pi를 켭니다.

![켜기](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> Raspberry가 정상 작동하는 데 충분한 전원이 공급되도록 2A 이상인 키트의 전원 공급 장치를 사용하는 것이 좋습니다.

## <a name="connect-raspberry-pi-3-to-the-network"></a>Raspberry Pi 3을 네트워크에 연결
Pi를 유선 네트워크 또는 무선 네트워크에 연결합니다. Pi가 컴퓨터와 같은 네트워크에 연결되어 있어야 합니다. 예를 들어 Pi를 컴퓨터가 연결된 스위치와 같은 스위치에 연결할 수 있습니다.

### <a name="connect-to-a-wired-network"></a>유선 네트워크에 연결
이더넷 케이블을 사용하여 Pi를 유선 네트워크에 연결합니다. 연결이 수립되면 Pi의 LED 두 개가 켜집니다.

![이더넷 케이블을 사용하여 연결](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>무선 네트워크에 연결
Raspberry Pi Foundation의 [지침](https://www.raspberrypi.org/learning/software-guide/wifi/)에 따라 Pi를 무선 네트워크에 연결합니다. 이 지침에 따르면 먼저 모니터와 키보드를 Pi에 연결해야 합니다.

## <a name="connect-the-led-to-pi"></a>LED를 Pi에 연결
이 작업을 완료하려면 [실험용 회로판](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), 커넥터 와이어, LED 및 저항기를 사용합니다. 이러한 항목을 Pi의 [범용 입출력](https://www.raspberrypi.org/documentation/usage/gpio/)(GPIO) 포트에 연결합니다.

![실험용 회로판, LED 및 저항기](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. LED의 더 짧은 다리를 **GPIO GND(Pin 6)**에 연결합니다.
2. 저항기의 한 쪽 다리에 더 긴 LED 다리를 연결합니다.
3. LED의 다른 다리를 **GPIO 4(Pin 7)**에 연결합니다.

LED 극성이 중요합니다. 이 극성 설정은 일반적으로 활성(낮음)이라고 합니다.

![핀아웃](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

축하합니다. Pi 구성을 마쳤습니다.

## <a name="summary"></a>요약
이 문서에서는 Raspbian을 설치하고, Pi를 네트워크에 연결하고, LED를 Pi에 연결하여 Pi를 구성하는 방법을 알아보았습니다. 아직 LED에는 불이 들어오지 않습니다. 다음 작업은 Pi에서 샘플 응용 프로그램 실행을 위한 준비로 필요한 도구 및 소프트웨어를 설치하는 것입니다.

![하드웨어는 준비되었습니다.](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>다음 단계
[도구 얻기](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)




<!--HONumber=Jan17_HO4-->


