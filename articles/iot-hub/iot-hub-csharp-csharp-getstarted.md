<properties
	pageTitle="C#용 Azure IoT Hub 시작 | Microsoft Azure"
	description="이 자습서에 따라 C#로 Azure IoT Hub 사용을 시작할 수 있습니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# .NET용 Azure IoT Hub 시작

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. IoT 프로젝트가 직면하는 큰 과제 중 하나는 장치를 안정적이고 안전하게 솔루션 백 엔드에 연결하는 방법입니다. 이러한 문제를 해결하기 위해 IoT Hub는 다음을 수행합니다.

- 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 스케일 메시징을 제공합니다.
- 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호합니다.
- 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Azure 포털을 사용하여 IoT Hub를 만듭니다.
- IoT Hub에서 장치 ID를 만듭니다.
- 클라우드 백 엔드로 원격 분석을 보내고, 클라우드 백 엔드으로부터 명령을 받는 시뮬레이션된 장치를 만듭니다.

이 자습서의 끝 부분에서 다음의 세 가지 Windows 콘솔 응용 프로그램이 만들어집니다.

* **CreateDeviceIdentity**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치에 연결합니다.
* **ReadDeviceToCloudMessages**는 시뮬레이션된 장치에서 보낸 원격 분석을 표시합니다.
* **SimulatedDevice**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 매초마다 원격 분석 메시지를 보냅니다.

> [AZURE.NOTE] [IoT Hub Sdk][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 SDK에 관한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

## IoT Hub 만들기

시뮬레이션된 장치를 연결하려면 IoT Hub를 만들어야 합니다. 다음 단계는 Azure 포털을 사용하여 이 작업을 완료하는 방법을 보여줍니다.

1. [Azure 포털][lnk-portal]에 로그인합니다.

2. 점프 모음에서 **새로 만들기**를 클릭하고 **사물 인터넷**을 클릭한 다음 **Azure IoT Hub**를 클릭합니다.

    ![][1]

3. **IoT Hub** 블레이드에서 IoT Hub의 구성을 선택합니다.

    ![][2]

    * **이름** 상자에 IoT Hub의 이름을 입력합니다. **이름**의 유효하고 사용 가능하면 **이름** 상자에 녹색 확인 표시가 나타납니다.
    * **가격 및 크기 조정 계층**을 선택합니다. 이 자습서에는 특정 계층이 필요하지 않습니다.
    * **리소스 그룹**에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][lnk-resource-groups]를 참조하세요.
    * **위치**에서 IoT hub를 호스트하는 위치를 선택합니다.  

4. IoT hub 구성 옵션을 선택한 경우 **만들기**를 클릭합니다. Azure가 IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드 또는 알림 패널에서 진행률을 모니터링할 수 있습니다.

    ![][3]

5. IoT Hub가 성공적으로 만들어지면, 새 IoT Hub의 블레이드를 열고, **호스트 이름**를 기록한 다음 **키** 아이콘을 클릭합니다.

    ![][4]

6. **iothubowner** 정책을 클릭한 다음, **iothubowner** 블레이드에 있는 연결 문자열을 복사하여 기록해둡니다.

    ![][5]

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 연결 문자열을 갖게 되었습니다.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ReadDeviceToCloudMessages** 및 **SimulatedDevice** 프로젝트 모두에 대한 **동작**으로 **시작**을 선택합니다.

   	![][41]

2.	**F5**를 눌러 두 응용 프로그램 실행을 시작합니다. **SimulatedDevice** 앱에서 출력된 콘솔은 시뮬레이션된 장치가 IoT Hub에 보낸 메시지를 보여주고, **ReadDeviceToCloudMessages** 앱에서 출력된 콘솔은 IoT Hub가 수신한 메시지를 보여줍니다.

   	![][42]

## 다음 단계

이 자습서에서 포털에서 새 IoT Hub를 구성한 다음, 허브의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 허브로 보내는 시뮬레이션된 장치에서 이 장치 ID를 사용하고 허브에서 받은 메시지를 표시하는 또 다른 앱을 만들었습니다. IoT Hub 기능 및 다른 IoT 시나리오는 다음의 자습서에서 계속해서 탐색할 수 있습니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d-tutorial]는 장치에 메시지를 보내고 IoT Hub에서 생성된 전달 피드백을 처리하는 방법을 보여줍니다.
- [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.
- [장치에서 파일 업로드][lnk-upload-tutorial]는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0323_2016-->