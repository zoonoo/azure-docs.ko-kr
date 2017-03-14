---
title: "Azure IoT Hub 시작 | Microsoft Docs"
description: "IoT Hub 서비스를 시작하는 방법"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>Azure IoT Hub 또는 Azure IoT Gateway SDK 시작

IoT Hub 서비스 또는 Gateway SDK로 시작하는 여러 자습서 중 하나를 선택할 수 있습니다.

## <a name="iot-hub"></a>IoT 허브

Azure IoT Hub는 수백만의 IoT(사물 인터넷) 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다.

IoT Hub 서비스를 시작하기 위해 다음을 수행할 수 있습니다.

- 개발 컴퓨터에서 실행 중인 시뮬레이션된 장치를 사용하는 자습서를 수행합니다. 기본 프로그래밍 언어([.NET][lnk-dotnet], [Java][lnk-java] 또는 [Node.js][lnk-nodejs])를 사용하는 시작 자습서를 선택합니다.

- 물리적 장치를 사용하는 자습서를 수행합니다. 기본 하드웨어 플랫폼([Raspberry Pi][lnk-rasp-pi], [Intel Edison][lnk-edison] 또는 [Arduino][lnk-arduino])을 사용하는 시작 자습서를 선택합니다. 이러한 자습서는 하드웨어 장치를 가져오는 방법에 대한 정보를 포함합니다.

- [C용 Azure IoT 장치 SDK 소개][lnk-c-intro] 문서에서 C 언어를 사용하여 IoT 장치 개발에 대해 참조하세요.

## <a name="gateway-sdk"></a>Gateway SDK

Gateway SDK를 사용하여 사용자 지정 필드 게이트웨이를 만들 수 있습니다. 게이트웨이는 분석을 실행하고 대기 시간을 줄일 수 있는 시간이 중요한 결정을 내리며 장치 관리 서비스를 제공하고 보안 및 개인 정보 제약 조건을 적용하고 프로토콜 변환을 수행하는 것과 같은 작업을 수행합니다.

Gateway SDK를 시작하기 위해 다음을 수행할 수 있습니다.

- 개발 컴퓨터에서 실행 중인 시뮬레이션된 게이트웨이를 사용하는 자습서를 수행합니다. [Linux][lnk-linux] 또는 [Windows][lnk-windows]에 대한 시작 자습서를 선택할 수 있습니다.

- 물리적 장치를 사용하는 자습서를 수행합니다. [Intel NUC(Next Unit of Computing)로 시뮬레이션된 장치][lnk-gateway-sim]를 사용하거나 [Intel NUC를 사용하는 SensorTag 장치][lnk-gateway-tag]를 사용하는 시작 자습서를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작 자습서를 마치면 [개발자 가이드][lnk-devguide] 및 [방법][lnk-howto] 자습서에서 IoT Hub 및 Gateway SDK의 더 많은 기능을 탐색할 수 있습니다.

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

