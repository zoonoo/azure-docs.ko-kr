---
title: IoT 게이트웨이 뒤에서 관리되는 장치 사용 | Microsoft Docs
description: IoT Hub를 통해 관리되는 장치와 함께 Gateway SDK를 사용하여 생성된 IoT Gateway 사용에 대한 안내 항목
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: cstreet

---
# IoT 게이트웨이 뒤에서 관리되는 장치 사용
## 기본 장치 격리
조직에서 IoT 솔루션에 대한 전반적인 보안을 강화하기 위해 IoT 게이트웨이를 사용하는 경우가 있습니다. 일부 장치는 클라우드에 데이터를 보내야 하지만 인터넷 상의 위협을 자체적으로 보호할 수는 없습니다. 장치가 게이트웨이를 통해 통신하도록 만들어서 외부 위협으로부터 장치를 보호할 수 있습니다.

게이트웨이는 안전한 환경과 공개 인터넷 사이의 경계에 위치합니다. 장치는 게이트웨이에 메시지를 전송하고, 게이트웨이는 그 메시지를 올바른 클라우드 대상에 전달합니다. 게이트웨이는 외부 위협에 대한 방비가 되어 있고, 인증되지 않은 요청을 차단하며, 인증된 인바운드 트래픽을 허용하고, 인바운드 트래픽을 올바른 장치에 전달합니다.

![][1]

추가적인 보안 계층을 위해 게이트웨이 뒤에 자체 보호가 가능한 장치를 배치할 수도 있습니다. 이 시나리오에서는 모든 장치의 OS를 업데이트하는 대신, 게이트웨이 OS를 최신 취약성에 대해 패치된 상태로만 유지하면 됩니다.

## 격리 및 인텔리전스
강화된 라우터는 장치를 격리만 하기에는 충분한 게이트웨이입니다. 하지만 IoT 솔루션은 단지 장치를 격리하기 것 이상의 인텔리전스를 제공하는 게이트웨이를 필요로 하는 경우가 있습니다. 예를 들어, 장치를 클라우드에서 관리해야 하는 경우가 있습니다. 솔루션의 클라우드 관리 부분으로, LWM2M 즉, 표준 장치 관리 프로토콜을 사용할 수 있습니다. 하지만, 장치는 TCP/IP가 아닌 프로토콜을 사용하여 원격 분석을 보냅니다. 또한, 장치에서는 데이터가 많이 생성되지만 필터링된 원격 분석 하위 집합만을 업로드하려고 합니다. 서로 다른 두 개의 데이터 스트림을 처리할 수 있는 IoT 게이트웨이 솔루션을 구축할 수 있습니다. 게이트웨이에 필요한 것은 다음과 같습니다.

* **원격 분석**을 이해하고, 필터링한 다음, 게이트웨이를 통해 클라우드에 업로드합니다. 게이트웨이는 장치와 클라우드 사이에서 데이터를 전달만 하는 단순한 라우터가 더 이상 아닙니다.
* 장치와 클라우드 사이에서 **LWM2M 장치 관리 데이터**를 교환합니다. 게이트웨이는 들어오는 데이터를 이해할 필요가 없으며, 장치와 클라우드 사이에서 데이터가 전달되어 들어오고 나가도록 보장하기만 하면 됩니다.

다음 그림은 이 시나리오를 보여줍니다.

![][2]

## 솔루션: Azure IoT 장치 관리 및 Gateway SDK
[Azure IoT 장치 관리][lnk-device-management] 공개 미리 보기 릴리스와 [Azure IoT Gateway SDK] 베타 릴리스를 사용하면 이 시나리오가 가능합니다. 게이트웨이는 각 데이터 스트림을 다음과 같이 처리합니다.

* **원격 분석**: Gateway SDK를 사용하여 원격 분석 데이터를 이해하고, 필터링하고 클라우드에 보내는 파이프라인을 만들 수 있습니다. Gateway SDK는 개발자 대신 이런 파이프라인의 일부를 구현하는 코드를 제공합니다. SDK 아키텍처에 자세한 정보는 [IoT Gateway SDK - 시작][lnk-gateway-get-started] 자습서를 참조하세요.
* **장치 관리**: Azure 장치 관리는 장치에 대한 관리 명령을 생성하기 위해 장치는 물론 클라우드 인터페이스로 실행되는 LWM2M 클라이언트를 제공합니다.
  
  장치와 IoT Hub 사이에 교환되는 LWM2M 데이터를 처리할 필요가 없기 때문에 게이트웨이에 특별한 논리가 필요하지 않습니다. LWM2M 데이터 교환을 활성화하기 위해서, 게이트웨이에 인터넷 연결 공유(현재 많은 운영 체제에 있는 기능)를 활성화할 수 있습니다. 게이트웨이 SDK는 다양한 운영 체제를 지원하기 때문에, 이 시나리오에 적합한 운영 체제를 선택할 수 있습니다. 지원되는 많은 운영 체제 중 두 가지 즉, [Windows 10] 및 [Ubuntu]에서 인터넷 연결 공유를 활성화하는 지침이 여기에 제공됩니다.

다음 그림은 [Azure IoT 장치 관리][lnk-device-management] 및 [Azure IoT Gateway SDK]를 사용하여 이 시나리오를 가능하게 만드는 데 사용되는 고급 아키텍처를 보여줍니다.

![][3]

## 다음 단계
Gateway SDK 사용법에 대해 자세히 알아보려면 다음 자습서를 참조하세요.

* [IoT Gateway SDK - Linux를 사용하여 시작][lnk-gateway-get-started]
* [IoT Gateway SDK – Linux를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기][lnk-gateway-simulated]

IoT Hub를 사용하는 장치 관리 기능에 대해 알아보려면 [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-library-c]를 참조하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [솔루션 디자인][lnk-design]
* [개발자 가이드][lnk-devguide]
* [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
* [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/ko-KR/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->