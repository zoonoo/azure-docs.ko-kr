<properties
	pageTitle="IoT Hub 시작 | Microsoft Azure"
	description="이 자습서에 따라 C#로 Azure IoT Hub 사용을 시작할 수 있습니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# IoT Hub 시작

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. IoT 프로젝트가 직면하는 큰 과제 중 하나는 장치를 안정적이고 안전하게 백 엔드 응용 프로그램에 연결하는 방법입니다. 이 시나리오를 단순화하려면 Azure IoT Hub는 안정적인 장치-클라우드 및 클라우드-장치 하이퍼-메시징을 제공하고 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 보안 통신을 사용하며 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

이 자습서에서는 Azure 포털을 사용하여 IoT Hub를 만드는 방법을 보여 줍니다. 또한 IoT Hub에 장치 ID를 만들고 장치-클라우드 메시지를 보내는 시뮬레이션된 장치를 만들며 클라우드 백 엔드에서 이러한 메시지를 수신하는 방법을 보여줍니다.

이 자습서의 끝 부분에서 다음의 세 가지 Windows 콘솔 응용 프로그램을 만듭니다.

* **CreateDeviceIdentity**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치에 연결합니다.
* **ReadDeviceToCloudMessages**는 장치-클라우드 메시지를 읽고 해당 콘텐츠를 표시하며
* **SimulatedDevice**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 매초마다 장치-클라우드 메시지를 보냅니다.

> [AZURE.NOTE]IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요. Java 및 노드용 Azure IoT 서비스 SDK는 곧 제공됩니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank")을 참조하세요.

## IoT Hub 만들기

1. [Azure Preview 포털]에 로그인합니다.

2. 점프 모음에서 **새로 만들기**를 클릭하고 **사물 인터넷**을 클릭한 다음 **IoT Hub**를 클릭합니다.

   	![][1]

3. **새 IoT Hub** 블레이드에서 원하는 IoT Hub의 구성을 지정합니다.

   	![][2]

    * **이름** 상자에 이름을 입력하여 IoT Hub를 식별합니다. **이름**의 유효성이 검사되면 **이름** 상자에 녹색 확인 표시가 나타납니다.
    * **가격 책정 및 규모 계층**을 원하는 대로 변경합니다. 이 자습서에는 특정 계층이 필요하지 않습니다.
    * **리소스 그룹**에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.
    * **위치**를 사용하여 IoT Hub를 호스트하는 지리적 위치를 지정합니다.  


4. 새 IoT Hub를 구성했으면 **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다. 그렇지 않은 경우 알림 섹션에서 진행률을 모니터링할 수 있습니다.

    ![][3]


5. IoT Hub를 성공적으로 만든 후에 새 IoT Hub의 블레이드를 열고 URI를 기록해 두며 위쪽에서 **키** 아이콘을 선택합니다.

   	![][4]

6. **iothubowner**라는 공유 액세스 정책을 선택한 다음 오른쪽 블레이드에서 연결 문자열을 복사하고 기록해 둡니다.

   	![][5]

이제 IoT Hub가 만들어 지고 이 자습서를 완료하는 데 필요한 URI 및 연결 문자열이 있습니다.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Visual Studio 내에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정...**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ProcessDeviceToCloudMessages** 및 **SimulatedDevice** 앱 모두에 **시작** 동작을 선택합니다.

   	![][41]

2.	**F5**를 누르고 응용 프로그램 시작 및 시뮬레이션된 앱에서 전송되고 프로세서 응용 프로그램에서 수신된 메시지를 모두 확인해야 합니다.

   	![][42]

## 다음 단계

이 자습서에서 새 IoT Hub를 설정하며 이는 허브의 ID 레지스트리에 장치 ID를 만들고 이 ID를 사용하여 장치-클라우드 메시지를 보내는 시뮬레이션된 장치를 프로그래밍합니다. IoT Hub 기능 및 시나리오는 다음의 자습서와 함께 계속해서 탐색할 수 있습니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기]는 장치에 메시지를 보내고 IoT Hub에서 생성된 전달 피드백을 처리하는 방법을 보여줍니다.
- [장치-클라우드 메시지 처리]\: 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여줍니다.
- [장치에서 파일 업로드]\: 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Azure Preview 포털]: https://portal.azure.com/

[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[장치에서 파일 업로드]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 개발자 센터]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->