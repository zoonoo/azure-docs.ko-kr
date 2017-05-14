---
title: "Azure IoT Hub - 클라우드에 IoT 장치 연결 시작 | Microsoft Docs"
description: "Azure IoT Hub에 IoT 장치를 연결하는 방법을 알아봅니다. 장치는 원격 분석을 IoT Hub로 전송할 수 있고 Iot Hub는 사용자 장치를 모니터링하고 관리할 수 있습니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT Hub 자습서"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT Hub 시작 자습서

Azure IoT Hub 및 Azure IoT 장치 SDK를 사용하여 IoT(사물 인터넷) 솔루션을 구축할 수 있습니다.

* Azure IoT Hub는 IoT 장치를 안전하게 연결하고, 모니터링하고, 관리하는 클라우드의 완전히 관리되는 서비스입니다. Azure IoT 장치 SDK를 사용하여 IoT 장치를 구현합니다.
* 레거시 장치, 대역폭 비용, 보안 및 개인 정보 보호 정책 또는 Edge 데이터 처리 등의 요소를 고려해야 하는 좀 더 복잡한 IoT 시나리오에서는 IoT 게이트웨이를 사용합니다. 이러한 시나리오에서는 Azure IoT Gateway SDK를 사용하여 장치를 IoT Hub에 연결하는 게이트웨이 장치를 구축합니다.

## <a name="what-do-the-tutorials-cover"></a>자습서에 포함된 내용

이러한 자습서는 Azure IoT Hub 및 장치 SDK를 소개합니다. 이 자습서에서는 IoT Hub의 기능을 설명하기 위한 일반적인 IoT 시나리오를 다룹니다. 또한 IoT Hub를 다른 Azure 서비스 및 도구와 결합하여 좀 더 강력한 IoT 솔루션을 구축하는 방법도 보여 줍니다. 이 자습서에서는 시뮬레이트된 IoT 장치 또는 실제 IoT 장치를 사용하도록 선택할 수 있습니다. 또한 게이트웨이를 사용하여 장치를 IoT Hub에 연결하는 방법도 알아볼 수 있습니다.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>장치 설치 시나리오: IoT 장치 또는 게이트웨이를 Azure IoT Hub에 연결

시작하려면 실제 또는 시뮬레이트된 장치를 선택할 수 있습니다.

| IoT 장치                       | 프로그래밍 언어 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| 시뮬레이션된 장치                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |

또한 게이트웨이를 사용하여 장치를 IoT Hub에 연결할 수 있습니다.

| 게이트웨이 장치               | 프로그래밍 언어 | 플랫폼         |
|------------------------------|----------------------|------------------|
| Intel NUC(모델 DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| 시뮬레이트된 게이트웨이            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>확장된 IoT 시나리오: 다른 Azure 서비스 및 도구 사용

장치를 IoT Hub에 연결할 때 다른 Azure 도구 및 서비스를 사용하는 추가 시나리오를 탐색할 수 있습니다.

| 시나리오                                    | Azure 서비스 또는 도구              |
|---------------------------------------------|------------------------------------|
| [IoT Hub 메시지 관리][Mg_IoT_Hub_Msg]                    | iothub-explorer tool               |
| [IoT 장치 관리][Mg_IoT_Dv]               | iothub-explorer tool               |
| [Save IoT Hub messages to Azure storage][Sv_IoT_Msg_Stor]                      | Azure Table Storage               |
| [센서 데이터 시각화][Vis_Data]             | Microsoft Power BI, Azure Web Apps |
| [센서 데이터를 사용하여 날씨 예측][Weather_Forecast] | Azure 기계 학습             |
| [자동 변칙 검색 및 반응][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>다음 단계

이러한 자습서를 완료한 후 [개발자 가이드][lnk-dev-guide]에서 IoT Hub의 기능을 추가로 탐색할 수 있습니다. [방법][lnk-how-to] 섹션에서 추가 자습서를 찾을 수 있습니다.


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
