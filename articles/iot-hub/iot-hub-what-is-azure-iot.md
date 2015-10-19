<properties
 pageTitle="Microsoft Azure IoT Hub 및 IoT(사물 인터넷) | Microsoft Azure"
 description="샘플 솔루션 아키텍처 및 이를 Azure IoT Hub, 장치 SDK 및 미리 구성된 솔루션과 연결하는 방법을 포함한 Azure IoT 개요"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 이 설명서의 범위

이러한 Azure 및 IoT 문서에서는 Microsoft IoT 플랫폼에 기반한 자체 IoT 솔루션 구현에 도움이 될 수 있는 두 가지 리소스 컬렉션에 대해 중점적으로 설명합니다.

- Azure IoT Hub
- Azure IoT 장치 SDK

또한 [Azure IoT Suite][lnk-iot-suite]에 관심이 있을 수 있습니다. 이는 원격 모니터링, 자산 관리 및 예측 유지 관리와 같은 일반적인 IoT 시나리오를 해결하기 위해 IoT 프로젝트의 크기를 조정하고 빠르게 시작할 수 있도록 해주는 미리 구성된 솔루션의 컬렉션입니다.

### Azure IoT Hub

IoT Hub는 장치에서 대규모로 장치-클라우드 데이터를 수신하고 해당 데이터를 스트림 이벤트 프로세서로 라우트할 수 있도록 해주는 Azure 서비스입니다. IoT Hub는 장치별 큐를 사용하여 특정 장치에 클라우드-장치 명령을 보낼 수도 있습니다.

또한 IoT Hub 서비스에는 장치를 프로비전하고 IoT Hub에 연결할 수 있는 장치를 관리하는 데 도와주는 장치 ID 레지스트리가 포함되어 있습니다.

### Azure IoT 장치 SDK

Microsoft에서는 다양한 장치 하드웨어 플랫폼과 운영 체제에서 실행되는 클라이언트 응용 프로그램을 구현할 수 있도록 해주는 IoT 장치 SDK를 제공합니다. IoT 장치 SDK에는 IoT Hub로 장치-클라우드 원격 분석 데이터 전송 및 IoT Hub에서 클라우드-장치 명령 수신을 용이하게 하는 라이브러리가 포함되어 있습니다. 이러한 IoT 장치 SDK를 사용하면 다양한 네트워크 프로토콜 중에서 선택하여 Azure IoT Hub와 통신할 수 있습니다.

## 다음 단계

Azure에서 IoT를 시작하려면 다음 리소스를 탐색하세요.

- [IoT Hub 시작][lnk-getstarted]
- [Azure IoT Hub][lnk-iot-hub]
- [Azure IoT Suite][lnk-iot-suite]  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/

<!---HONumber=Oct15_HO2-->