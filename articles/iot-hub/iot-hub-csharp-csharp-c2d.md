<properties
	pageTitle="IoT Hub를 사용하여 클라우드-장치 메시지 보내기 | Microsoft Azure"
	description="이 자습서에 따라 C#에서 Azure IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법을 알아봅니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 자습서: IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. [IoT Hub 시작] 자습서에서 IoT Hub를 만들어 그 안에 장치 ID를 프로비전하고 장치-클라우드 메시지를 보내는 시뮬레이트된 장치를 코딩하는 방법을 볼 수 있습니다.

이 자습서는 [IoT Hub 시작]을 기반으로 하며 단일 장치에 클라우드-장치 메시지를 보내는 방법 및 IoT Hub에서 배달 승인(*피드백*)을 요청하고 응용 프로그램 클라우드 백 엔드에서 이를 수신하는 방법을 보여 줍니다.

클라우드-장치 메시지에 자세한 정보는 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - C2D]에서 찾아볼 수 있습니다.

이 자습서의 끝 부분에서는 다음 두 개의 Windows 콘솔 응용 프로그램을 실행합니다.

* **SimulatedDevice**, [IoT Hub 시작]에서 만든 앱의 수정된 버전으로서 IoT Hub에 연결하고 클라우드-장치 메시지를 수신합니다.
* **SendCloudToDevice**, IoT Hub를 통해 시뮬레이트된 장치에 클라우드-장치 메시지를 보낸 다음 배달 승인을 받습니다.

> [AZURE.NOTE] IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요. Java 및 노드용 Azure IoT 서비스 SDK는 출시 예정입니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank")을 참조하세요.

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## 다음 단계

이 자습서에서 클라우드-장치 메시지를 보내고 받는 방법을 알아보았습니다. 다음 자습서에서 IoT Hub 기능 및 시나리오를 계속해서 탐색할 수 있습니다.

- [장치-클라우드 메시지 처리]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.
- [장치에서 파일 업로드]는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[장치에서 파일 업로드]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 개발자 센터]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0128_2016-->