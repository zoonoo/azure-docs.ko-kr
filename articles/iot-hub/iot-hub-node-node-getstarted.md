<properties
	pageTitle="Node.js용 Azure IoT Hub 시작 | Microsoft Azure"
	description="Node.js용 Azure IoT Hub 시작 자습서입니다. Microsoft Azure IoT SDK를 포함한 Azure IoT Hub 및 Node.js를 사용하여 사물 인터넷의 솔루션을 구현합니다."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Node.js용 Azure IoT Hub 시작

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 소개

Azure IoT Hub는 수백만의 IoT(사물 인터넷) 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. IoT 프로젝트가 직면하는 큰 과제 중 하나는 장치를 안정적이고 안전하게 솔루션 백 엔드에 연결하는 방법입니다. 이러한 문제를 해결하기 위해 IoT Hub는 다음을 수행합니다.

- 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 스케일 메시징을 제공합니다.
- 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호합니다.
- 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Azure 포털을 사용하여 IoT Hub를 만듭니다.
- IoT Hub에서 장치 ID를 만듭니다.
- 클라우드 백 엔드에 원격 분석을 전송하는 시뮬레이션된 장치를 만듭니다.

이 자습서의 끝 부분에서 다음의 세 가지 Node.js 콘솔 응용 프로그램이 만들어집니다.

* **CreateDeviceIdentity.js**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치에 연결합니다.
* **ReadDEviceToCloudMessages.js**는 시뮬레이션된 장치에서 보낸 원격 분석을 표시합니다.
* **SimulatedDevice.js**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 AMQPS 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

> [AZURE.NOTE] [IoT Hub Sdk][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 SDK에 관한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Node.js 버전 0.12.x 이상 <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Node.js을 설치하는 방법을 설명합니다.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

마지막 단계로 IoT Hub 블레이드에서 **설정**을 클릭한 다음 **설정** 블레이드에서 **메시징**을 클릭합니다. **메시징** 블레이드에서 **이벤트 허브 호환 이름** 및 **이벤트 허브 호환 끝점**을 기록해 둡니다. **read-d2c-messages** 응용 프로그램을 만들 때 이러한 값이 필요합니다.

    ![][6]

이제 IoT Hub를 만들었고 이 자습서의 나머지 부분을 완료해야 할 IoT Hub 호스트 이름, IoT Hub 연결 문자열, 이벤트 허브 호환 이름 및 이벤트 허브 호환 끝점 값이 있습니다.

[AZURE.INCLUDE [iot-hub-get-started-cloud-node](../../includes/iot-hub-get-started-cloud-node.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-node](../../includes/iot-hub-get-started-device-node.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub 모니터링을 시작합니다.

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석 데이터 전송을 시작합니다.

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure 포털][lnk-portal]의 **사용 현황** 타일은 허브로 전송된 메시지의 수를 보여줍니다.

    ![][43]

## 다음 단계

이 자습서에서 포털에서 새 IoT Hub를 구성한 다음, 허브의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 허브로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용하고 허브에서 받은 메시지를 표시하는 앱을 만들었습니다. IoT Hub 기능 및 다른 IoT 시나리오는 다음의 자습서에서 계속해서 탐색할 수 있습니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d-tutorial]는 장치에 메시지를 보내고 IoT Hub에서 생성된 전달 피드백을 처리하는 방법을 보여줍니다.
- [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.
- [장치에서 파일 업로드][lnk-upload-tutorial]는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0413_2016-->