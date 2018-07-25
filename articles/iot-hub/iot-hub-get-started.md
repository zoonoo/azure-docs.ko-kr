---
title: Azure IoT Hub - 클라우드에 IoT 장치 연결 시작 | Microsoft Docs
description: Azure IoT Hub에 IoT 보드 및 시작 키트를 연결하는 방법을 알아봅니다. 장치는 원격 분석을 IoT Hub로 전송할 수 있고 Iot Hub는 사용자 장치를 모니터링하고 관리할 수 있습니다.
author: dominicbetts
manager: timlt
keywords: Azure IoT Hub 자습서
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125195"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>실제 장치로 Azure IoT Hub 시작

이러한 방법 문서는 Azure IoT Hub를 소개하고, 실제 장치에서 장치 SDK를 실행하는 방법을 설명합니다.

## <a name="set-up-your-device"></a>장치 설정

IoT 장치 또는 게이트웨이를 Azure IoT Hub에 연결합니다.

| IoT 장치                       | 프로그래밍 언어 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [VSCode의 Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| 온라인 장치 시뮬레이터         | [Raspberry Pi(Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
