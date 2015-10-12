<properties
	pageTitle="IoT Hub 클라우드-장치 메시지를 처리 | Microsoft Azure"
	description="이 자습서를 수행하여 IoT Hub 장치-클라우드 메시지를 처리하는 데 유용한 패턴을 알아봅니다."
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

# IoT Hub 장치-클라우드 메시지 처리

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 이전 자습서([IoT Hub로 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능 및 장치와 클라우드 구성 요소에서 해당 기능에 액세스하는 방법을 설명합니다.

이 자습서는 [IoT Hub로 시작]에 나타난 코드에 빌드하여 장치-클라우드 메시지를 처리하기 위한 두 가지 패턴을 제공합니다.

첫 번째 패턴은 [Azure Blob]에서 장치-클라우드 메시지의 신뢰할 수 있는 저장소입니다. *콜드 경로* 분석을 구현하는 경우 이 시나리오는 매우 일반적이며 여기서 blob에 저장된 데이터는 [Azure 데이터 팩터리] 또는 [Hadoop] 스택과 같은 도구로 제어되는 분석에 대한 입력으로 사용됩니다.

두 번째 패턴은 *대화형* 장치-클라우드 메시지의 신뢰할 수 있는 처리입니다. 장치-클라우드로 메시지는 분석 엔진에 공급되는 *데이터 요소* 메시지와 달리 응용 프로그램 백 엔드에서 일련의 작업에 즉각적인 트리거인 경우 *대화형*입니다. 예를 들어 CRM 시스템에서 티켓의 삽입을 트리거해야 하는 장치에서 보내는 경보는 *대화형* 장치-클라우드 메시지이며 이는 온도 샘플을 포함한 원격 분석 메시지와 달리 *데이터 요소* 메시지입니다.

IoT Hub가 이벤트 허브 호환 끝점을 노출하여 장치-클라우드 메시지를 받기 때문에 이 자습서에서는 [EventProcessorHost]를 사용하여 다음과 같은 이벤트 프로세서 클래스를 호스트합니다.

* 안전하게 Azure Blob에 *데이터 요소*를 저장하고
* 즉시 처리하기 위해 *대화형* 장치-클라우드 메시지를 [서비스 버스 큐]로 전달합니다.

[서비스 버스][Service Bus Queue]는 메시지 당 검사점 및 시간 창 기반 중복 제거 기능을 제공하므로 신뢰할 수 있게 대화형 메시지를 처리할 수 있는 방법입니다.

이 자습서의 끝 부분에서 다음의 세 가지 Windows 콘솔 응용 프로그램을 실행합니다.

* **SimulatedDevice**, [IoT Hub로 시작]에서 만든 수정된 버전의 앱은 초 마다 *데이터 요소* 장치-클라우드 메시지를 보내고 10초 마다 *대화형* 장치-클라우드 메시지를 보냅니니다.
* **ProcessDeviceToCloudMessages**는 [EventProcessorHost]를 사용하여 Azure blob에 *데이터 지점* 메시지를 안정적으로 저장하고 서비스 버스 큐에 *대화형* 메시지를 전달하며
* **ProcessD2cInteractiveMessages**는 큐에서 메시지를 제거합니다.

> [AZURE.NOTE]IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

> [AZURE.NOTE]이 자습서의 내용은 이벤트 허브와 호환되는 메시지를 사용하는 다른 방법, 즉 Storm과 같은 [Hadoop] 프로젝트에 직접 적용할 수 있습니다. 자세한 내용은 [IoT Hub 지침 - 이벤트 허브 호환성]을 참조하세요

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank")을 참조하세요.

또한 [Azure 저장소] 및 [Azure 서비스 버스]의 가정한 일부 지식입니다.


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Visual Studio 내에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정...**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ProcessDeviceToCloudMessages**, **SimulatedDevice** 및 **ProcessD2cInteractiveMessages** 앱에 **시작** 동작을 선택합니다.

2.	**F5** 키를 누르고 모든 응용 프로그램 시작이 표시되어야 합니다. 시뮬레이션 장치에서 보낸 모든 대화형 메시지는 대화형 메시지 프로세서에서 처리되어야 합니다.

  ![][50]

> [AZURE.NOTE]업데이트된 blob 파일을 보려면 `StoreEventProcessor`에서 `MAX_BLOCK_SIZE` 상수를 줄일 해여 상수을 더 낮출 수 없습니다.(즉, `1024`) 즉, 시뮬레이션된 장치에서 보낸 데이터로 블록 크기 제한에 도달하는데 시간이 걸리기 때문입니다. 해당 편집으로 blob가 저장소 컨테이너에서 생성되고 업데이트를 볼 수 있습니다.

## 다음 단계

이 자습서에서 [EventProcessorHost]를 사용하여 안정적으로 *데이터 요소* 및 *대화형* 장치-클라우드 메시지를 처리하는 방법을 알아보았습니다. Ananlogous 메시지 처리 논리를 함께 구현할 수 있습니다.

- [장치에서 파일 업로드]\: 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob]: https://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure 데이터 팩터리]: https://azure.microsoft.com/ko-kr/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/ko-kr/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/ko-kr/documentation/articles/service-bus-dotnet-how-to-use-queues/
[서비스 버스 큐]: https://azure.microsoft.com/ko-kr/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/ko-kr/library/hh680901(v=pandp.50).aspx

[IoT Hub 지침 - 이벤트 허브 호환성]: iot-hub-guidance.md#eventhubcompatible

[Azure 저장소]: https://azure.microsoft.com/ko-kr/documentation/services/storage/
[Azure 서비스 버스]: https://azure.microsoft.com/ko-kr/documentation/services/service-bus/

[Azure Preview Portal]: https://portal.azure.com/

[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[장치에서 파일 업로드]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub로 시작]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 개발자 센터]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->