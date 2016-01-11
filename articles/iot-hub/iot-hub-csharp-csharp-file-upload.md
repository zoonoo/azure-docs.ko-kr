<properties
	pageTitle="IoT Hub를 사용하여 장치에서 파일 업로드 | Microsoft Azure"
	description="이 자습서에 따라 C#과 함께 Azure IoT Hub를 사용하여 장치에서 파일을 업로드하는 방법을 알아봅니다."
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

# 자습서: IoT Hub를 사용하여 장치에서 클라우드로 파일을 업로드하는 방법

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 이전 자습서([IoT Hub로 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능 및 장치와 클라우드 구성 요소에서 해당 기능에 액세스하는 방법을 설명합니다. [장치-클라우드 메시지 프로세스]는 Azure blob 저장소에 장치-클라우드 메시지를 안정적으로 저장하는 방법을 설명했습니다. 그러나 장치에서 가져온 데이터가 상대적으로 작은 장치-클라우드 메시지 에 쉽게 매핑되지 않는 경우가 있습니다. 예제 일부는 빈도가 높은 이미지, 비디오, 진동 데이터 샘플 또는 특정 형태의 전처리된 데이터를 포함하는 큰 파일입니다. 이러한 파일은 일반적으로 [Azure 데이터 팩터리] 또는 [Hadoop] 스택과 같은 도구를 사용하여 배치 방식으로 처리됩니다. 장치에서 파일을 업로드하는 작업이 이벤트를 전송보다 우선하면 IoT Hub 보안 및 안정성 기능을 사용할 수 있습니다.

이 자습서는 [IoT Hub로 클라우드-장치 메시지 보내기]에 제공된 코드에서 빌드되어 클라우드-장치 메시지를 사용하고 파일을 업로드하는 데 쓰일 Azure blob IoT URI 장치에 안전하게 제공하는 방법 및 IoT Hub 전달 승인을 사용하여 앱 백 엔드에서 파일의 처리를 트리거하는 방법을 보여줍니다. 이 방법의 장점은 IoT Hub 장치 ID의 재사용, 클라우드-장치 메시지 전달 승인으로 파일이 성공적으로 업로드된 앱 백 엔드를 알립니다.

> [AZURE.NOTE]여기에 사용된 동일한 방법은 클라우드에서 장치가 파일을 안전하게 다운로드하는 데 사용할 수 있습니다.

클라우드-장치 메시지 및 IoT Hub 보안에 대한 자세한 정보는 [IoT 허브 개발자 가이드]에서 찾을 수 있습니다.

이 자습서의 끝 부분에서 다음 두 개의 Windows 콘솔 응용 프로그램을 실행합니다.

* **SimulatedDevice**: [IoT Hub로 클라우드-장치 메시지 보내기]에서 만든 앱의 수정된 버전으로서 IoT 허브에 연결하고 Azure Blob URI를 포함한 클라우드-장치 메시지를 수신합니다. 수신된 각 클라우드-장치 메시지의 경우 지정된 blob URI에 파일을 업로드를 트리거합니다.
* **SendCloudToDevice**는 Azure blob URI([Blob 서비스로 SAS 만들기 및 사용](../storage/storage-dotnet-shared-access-signature-part-2.md)에서 설명한 것처럼)를 빌드하고 IoT Hub를 통해 시뮬레이션된 장치에 클라우드-장치 메시지를 보낸 다음 해당 전달을 승인받습니다.

> [AZURE.NOTE]IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요. Java 및 노드용 Azure IoT 서비스 SDK는 출시 예정입니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank")을 참조하세요.


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.  Visual Studio 내에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정...**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **SimulatedDevice** 및 **SendCloudToDevice** 앱 모두에 대한 **시작** 동작을 선택합니다.

2.  **F5** 키를 누르고 응용 프로그램이 모두 시작하는지 확인해야 합니다. **SendCloudToDevice** 창을 선택하고 키를 누릅니다. 시뮬레이션된 장치가 파일을 업로드할 때 메시지를 출력하고 **SendCloudToDevice** 앱이 성공적인 피드백을 수신하였음을 표시합니다. [Azure 포털] 또는 Visual Studio 서버 탐색기를 사용하여 저장소 계정에 있는 파일의 현재 상태를 확인할 수 있습니다.

  ![][50]


## 다음 단계

이 자습서에서 장치에서 파일 업로드를 단순화하기 위해 클라우드-장치 메시지를 활용하는 방법을 알아보았습니다. IoT Hub 기능 및 시나리오는 다음의 자습서와 함께 계속해서 탐색할 수 있습니다.

- [장치-클라우드 메시지 처리]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md

[Azure 포털]: https://portal.azure.com/

[Azure 데이터 팩터리]: https://azure.microsoft.com/ko-KR/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/ko-KR/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub로 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[장치-클라우드 메시지 프로세스]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT 허브 개발자 가이드]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub로 시작]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 개발자 센터]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1223_2015-->