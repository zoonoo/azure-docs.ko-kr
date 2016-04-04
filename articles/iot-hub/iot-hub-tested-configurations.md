<properties
	pageTitle="OS 플랫폼 호환성| Microsoft Azure"
	description="OS 플랫폼과 IoT 장치 SDK 호환성을 요약합니다."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# OS 플랫폼의 장치 SDK 호환성

이 문서에서는 다양한 OS 플랫폼과의 SDK 호환성을 설명합니다. 어떤 장치를 사용할지 확인하지 않은 경우 이 문서의 [OS 플랫폼 및 라이브러리](#os-platforms) 호환성 섹션을 확인하세요.

## IoT용 Microsoft Azure Certified 프로그램

장치가 이미 있는 경우 호환성에 대한 장치 특정 정보를 찾으려면 [IoT용 Microsoft Azure Certified 프로그램][lnk-certified]에 포함된 장치 목록을 확인하세요. IoT용으로 인증된 Microsoft Azure는 광범위한 IoT 에코 시스템과 Microsoft Azure를 연결하여 개발자와 설계자는 호환성 시나리오를 이해할 수 있습니다. 특히 OS/장치 조합의 신뢰할 수 잇는 목록을 제공하여 개념 증명 또는 파일럿 단계에서든 IoT 프로젝트를 빠르게 시작할 수 있도록 합니다.

## OS 플랫폼

Azure IoT 라이브러리는 다음 운영 체제 플랫폼에서 테스트되었습니다.


|Linux/Unix OS 플랫폼 | 버전|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Windows OS 플랫폼 | 버전|
|:---------------|:------------:|
|Windows 데스크톱| 10 |
|Windows IoT 코어| 10 |
|Windows Server| 2012 R2|

|기타 플랫폼 | 버전|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## C 라이브러리

[C용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md)는 다음 구성에서 테스트되었습니다.

|OS 플랫폼| 버전|프로토콜|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |
|Fedora Linux| 20 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |
|Windows 데스크톱| 10 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Node.js 라이브러리

[Node.js용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md)는 다음 구성에서 테스트되었습니다.


|런타임| 버전|프로토콜|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |



## Java 라이브러리

[Java용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md)는 다음 구성에서 테스트되었습니다.

|런타임| 버전|프로토콜|
|:---------|:----------:|----|
|Java SE(Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE(Linux)| 1\.8 | HTTPS, AMQP, MQTT|

Java용 Microsoft Azure IoT 서비스 SDK는 다음 구성에서 테스트되었습니다.

|런타임| 버전|프로토콜|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

[.NET용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md)는 다음 구성에서 테스트되었습니다.

|OS 플랫폼| 버전|프로토콜|
|:---------|:----------:|:----------:|
|Windows 데스크톱| 10 | WebSocket을 통한 HTTPS, AMQP, MQTT, AMQP |
|Windows IoT 코어|10 | HTTPS |

관리된 에이전트 코드는 Microsoft .NET Framework 4.5가 필요함


## 다음 단계

- [IoT용 Microsoft Azure Certified][lnk-certified] 프로그램에 대해 알아봅니다.
- [IoT 장치에 대한 인증](http://azure.com/iotdev)을 사용하여 솔루션을 개발하는 방법에 대해 자세히 알아봅니다.


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->