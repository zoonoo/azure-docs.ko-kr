<properties
 pageTitle="사물 인터넷에 대한 Azure 솔루션 | Microsoft Azure"
 description="샘플 솔루션 아키텍처 및 이를 Azure IoT Hub, 장치 SDK 및 미리 구성된 솔루션과 연결하는 방법을 포함한 Azure IoT 개요"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 다음 단계

Azure IoT Hub는 응용 프로그램 백 엔드와 수백 만개의 IoT 장치 간에 안정적이고 신뢰할 수 있는 양방향 통신이 가능하도록 해주는 Azure 서비스입니다. 이를 통해 응용 프로그램 백 엔드는 장치로부터 대규모로 원격 분석을 수신하고 해당 데이터를 스트림 이벤트 프로세서로 라우팅하며 클라우드-장치 명령을 특정 장치로 보낼 수 있습니다. 사용자 고유의 솔루션 백 엔드를 구현하기 위해 IoT Hub를 사용할 수 있습니다. 또한 IoT Hub에는 장치, 보안 자격 증명, 허브에 연결하는 권한을 프로비전하는 데 사용된 장치 ID 레지스트리가 포함되어 있습니다. 자세한 내용은 다음을 참조하세요.

- [IoT Hub란?][lnk-iot-hub]
- [IoT Hub 시작][lnk-getstarted]

다양한 장치 하드웨어 플랫폼과 운영 체제에서 클라이언트 응용 프로그램을 구현하기 위해 IoT 장치 SDK를 제공할 수 있습니다. IoT 장치 SDK에는 IoT Hub로 원격 분석 전송 및 클라우드-장치 명령 수신을 용이하게 하는 라이브러리가 있습니다. SDK를 사용하면 다양한 네트워크 프로토콜 중에서 선택하여 IoT Hub와 통신할 수 있습니다. 자세한 내용은 [장치 SDK에 대한 정보][lnk-device-sdks]를 참조하세요.

또한 미리 구성된 솔루션의 컬렉션인 [Azure IoT Suite][lnk-iot-suite]에 관심이 있을 수도 있습니다. IoT Suite를 사용하면 원격 모니터링, 자산 관리 및 예측 유지 관리와 같은 일반적인 IoT 시나리오를 해결하기 위해 IoT 프로젝트의 크기를 조정하고 빠르게 시작할 수 있습니다.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0218_2016-->