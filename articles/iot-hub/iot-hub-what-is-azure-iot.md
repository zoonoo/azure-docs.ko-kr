---
title: "사물 인터넷을 위한 Azure 솔루션(IoT Suite) | Microsoft Docs"
description: "샘플 IoT 솔루션 아키텍처의 개요 및 장치에 연결된 방법, Azure IoT Hub 서비스, Azure IoT 장치 SDK, Azure IoT 서비스 SDK 및 기타 Azure 서비스입니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e95d02e706b87eaa355951a34bbaa3abf2ac2370


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>다음 단계
Azure IoT Hub는 솔루션 백 엔드와 수백 만개의 장치 간에 안정적이고 신뢰할 수 있는 양방향 통신이 가능하도록 해주는 Azure 서비스입니다. 이것을 사용하면 솔루션 백 엔드에서 다음 작업이 가능합니다.

* 장치로부터 대규모 원격 분석을 수신합니다.
* 장치에서 스트림 이벤트 프로세서로 데이터를 라우팅합니다.
* 장치로부터 파일 업로드를 수신합니다.
* 특정 장치에 클라우드-장치 메시지를 보냅니다.

사용자 고유의 솔루션 백 엔드를 구현하기 위해 IoT Hub를 사용할 수 있습니다. 또한 IoT Hub에는 장치, 보안 자격 증명, IoT Hub에 연결하는 권한을 프로비전하는 데 사용된 장치 ID 레지스트리가 포함되어 있습니다. IoT Hub에 대해 자세히 알아보려면 [IoT Hub란?][lnk-iot-hub]을 참조하세요.

Azure IoT Hub를 사용하여 표준 기반 장치 관리를 통해 원격으로 장치를 관리, 구성, 업데이트할 수 있는 방법을 알아보려면 [IoT Hub를 사용한 장치 관리의 개요][lnk-device-management]를 참조하세요.

다양한 장치 하드웨어 플랫폼과 운영 체제에서 클라이언트 응용 프로그램을 구현하기 위해 Azure IoT 장치 SDK를 제공할 수 있습니다. 장치 SDK에는 IoT Hub에 대한 원격 분석 전송 및 클라우드-장치 메시지 수신을 용이하게 하는 라이브러리가 있습니다. 장치 SDK를 사용하면 몇 가지 네트워크 프로토콜 중에서 선택하여 IoT Hub와 통신할 수 있습니다. 자세한 내용은 [장치 SDK에 대한 정보][lnk-device-sdks]를 참조하세요.

일부 코드를 작성하고 몇 가지 샘플을 실행하기 시작하려면 [IoT Hub 시작][lnk-getstarted] 자습서를 참조하세요.

또한 미리 구성된 솔루션의 컬렉션인 [Azure IoT Suite][lnk-iot-suite]에 관심이 있을 수도 있습니다. IoT Suite를 사용하면 원격 모니터링, 자산 관리 및 예측 유지 관리와 같은 일반적인 IoT 시나리오를 해결하기 위해 IoT 프로젝트의 크기를 조정하고 빠르게 시작할 수 있습니다.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Feb17_HO3-->


