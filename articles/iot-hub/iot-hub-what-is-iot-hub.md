<properties
 pageTitle="Azure IoT Hub 개요 | Microsoft Azure"
 description="IoT Hub, 장치 연결, 사물 인터넷 통신 패턴 및 서비스 보조 통신 패턴 정의를 포함한 Azure IoT Hub 서비스의 개요입니다."
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
 ms.date="08/25/2016"
 ms.author="dobett"/>

# Azure IoT Hub란?

Azure IoT Hub를 시작합니다. 이 문서에서는 Azure IoT Hub의 개요를 제공하고 IoT(사물 인터넷) 솔루션을 구현하기 위해 이 서비스를 사용해야 하는 이유에 대해 설명합니다. Azure IoT Hub는 수백만의 IoT 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. Azure IoT Hub:

- 대규모 환경에서 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 메시징을 제공합니다.
- 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호합니다.
- 장치 연결 및 장치 ID 관리 이벤트에 대한 포괄적인 모니터링을 제공합니다.
- 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

[IoT Hub와 이벤트 허브 비교][lnk-compare] 문서에서는 두 서비스 간의 주요한 차이를 설명하고 IoT 솔루션에서 loT Hub를 사용했을 때의 이점을 강조합니다.

![사물 인터넷 솔루션에서 클라우드 게이트웨이인 Azure IoT Hub][img-architecture]

> [AZURE.NOTE] IoT 아키텍처에 대한 자세한 설명은 [Microsoft Azure IoT 참조 아키텍처][lnk-refarch]를 참조하세요.

## IoT 장치 연결 과제

IoT Hub 및 장치 라이브러리를 통해 장치를 솔루션 백 엔드에 안정적이고 안전하게 연결하는 방법에 대한 과제를 해결할 수 있습니다. IoT 장치:

- 운영자가 없는 내장 시스템인 경우가 많습니다.
- 물리적 액세스에 많은 비용이 드는 원격 위치에 있을 수 있습니다.
- 솔루션 백 엔드를 통해서만 연결 가능합니다.
- 전원 및 리소스 처리 제한이 있을 수 있습니다.
- 일시적이거나 느리거나 비용이 많이 드는 네트워크 연결이 있을 수 있습니다.
- 독점, 사용자 지정 또는 업계 특정 응용 프로그램 프로토콜을 사용해야 할 수 있습니다.
- 널리 사용되는 다양한 하드웨어 및 소프트웨어 플랫폼을 사용하여 만들 수 있습니다.

위의 요구 사항 외에도 모든 IoT 솔루션은 크기 조정, 보안 및 안정성을 제공해야 합니다. 연결 요구 사항의 결과 집합은 웹 컨테이너 및 메시징 브로커와 같은 기존 기술을 사용하여 구현하는데 어렵고 많은 시간이 소요됩니다.

## Azure IoT Hub를 사용하는 이유

Azure IoT Hub는 다음과 같은 방법으로 장치 연결 과제를 해결합니다.

-   **장치별 인증 및 보안 연결**. IoT Hub에 연결할 수 있도록 각 장치를 고유한 [보안 키][lnk-devguide-security]로 프로비전할 수 있습니다. [IoT Hub ID 레지스트리][lnk-devguide-identityregistry]는 솔루션의 키와 장치 ID를 저장합니다. 솔루션 백 엔드는 개별 장치를 허용 목록 및 거부 목록에 추가하여 장치 액세스를 완전히 제어할 수 있습니다.

-   **장치 연결 작업에 대한 모니터링**. 장치 ID 관리 작업 및 장치 연결 이벤트에 대한 자세한 작업 로그를 받을 수 있습니다. 모니터링 기능을 통해 IoT 솔루션에서 잘못된 자격 증명을 사용하여 연결하려 하거나, 메시지를 너무 자주 보내거나, 모든 클라우드-장치 간 메시지를 거부하는 장치와 같은 연결 문제를 식별할 수 있습니다.

-   **광범위한 장치 라이브러리 집합**. [Azure IoT 장치 SDK][lnk-device-sdks]는 사용 가능하며 다양한 언어 및 플랫폼(여러 Linux 배포판, Windows 및 실시간 운영 체제에 대한 C)에 대해 지원됩니다. 또한 Azure IoT 장치 SDK에서는 C#, Java 및 JavaScript와 같은 관리된 언어를 지원합니다.

-   **IoT 프로토콜 및 확장성**. 솔루션이 장치 라이브러리를 사용할 수 없는 경우 IoT Hub는 장치가 기본적으로 MQTT v3.1.1, HTTP 1.1 및 AMQP 1.0 프로토콜을 사용할 수 있도록 허용하는 공용 프로토콜을 노출합니다. 다음을 수행하여 사용자 지정 프로토콜에 대한 지원을 제공하는 IoT Hub를 확장할 수도 있습니다.

    - IoT Hub가 이해하는 세 가지 프로토콜 중 하나에 사용자 지정 프로토콜을 변환하는 [Azure IoT 게이트웨이 SDK][lnk-gateway-sdk]로 필드 게이트웨이를 만듭니다.
    - 클라우드에서 실행되는 오픈 소스 구성 요소인 [Azure IoT 프로토콜 게이트웨이][protocol-gateway]를 사용자 지정합니다.

-   **확장**. Azure IoT Hub는 동시에 연결된 수백만 대의 장치 및 수백만 개의 초당 이벤트로 확장 가능합니다.

이러한 이점은 여러 통신 패턴에 대해 일반적으로 적용됩니다. 현재 IoT Hub를 통해 다음과 같은 특정 통신 패턴을 구현할 수 있습니다.

-   **이벤트 기반 장치-클라우드 수집.** IoT Hub는 장치에서 초당 수백만 개의 이벤트를 안정적으로 수신하고 이벤트 프로세서 엔진을 사용하여 실행 부하 과다 경로에서 이를 처리하거나 분석을 위해 콜드 경로에 저장할 수 있습니다. IoT Hub는 안정적인 처리를 보장하고 부하에서 최대치를 흡수하도록 최대 7일 동안 이벤트 데이터를 유지합니다.

-   **신뢰할 수 있는 클라우드-장치 메시징(또는 *명령*).** 솔루션 백 엔드는 IoT Hub를 사용하여 적어도 한 번 배달 보증으로 메시지를 개별 장치에 보낼 수 있습니다. 각 메시지는 개별 TTL(Time-to-Live) 설정이 있으며 백 엔드는 배달 및 만료 수신 확인을 모두 요청할 수 있습니다. 이러한 수신 확인은 클라우드-장치 메시지의 수명 주기 전체에 대한 가시성을 확보할 수 있습니다. 그런 다음 장치에서 실행하는 작업을 포함하는 비즈니스 논리를 구현할 수 있습니다.

-   **클라우드로 파일 및 캐싱된 센서 데이터를 업로드합니다.** 장치에서 IoT Hub에서 관리되는 SAS URI를 사용하여 Azure 저장소로 파일을 업로드할 수 있습니다. IoT Hub는 파일이 클라우드에 도착할 때 백 엔드에서 처리할 수 있도록 알림을 생성할 수 있습니다.

## 게이트웨이

loT 솔루션의 게이트웨이는 일반적으로 클라우드에 배포된 [프로토콜 게이트웨이][lnk-gateway] 또는 장치에 로컬로 배포된 [필드 게이트웨이][lnk-field-gateway]입니다. 프로토콜 게이트웨이는 MQTT에서 AMQP와 같은 프로토콜 변환을 수행합니다. 필드 게이트웨이는 에지에 분석을 실행하고 대기 시간을 줄일 수 있는 시간이 중요한 결정을 내리며 장치 관리 서비스를 제공하고 보안 및 개인 정보 제약 조건을 적용하고 프로토콜 변환을 수행할 수도 있습니다. 두 유형의 게이트웨이는 장치와 loT Hub 간의 중개자 역할을 합니다.

현장 게이트웨이는 일반적으로 솔루션에서 액세스 및 정보 흐름을 관리하는 실제 역할을 수행하므로 단순한 트래픽 라우팅 장치(예: 네트워크 주소 변환 장치 또는 방화벽)와는 다릅니다.

솔루션에는 프로토콜과 필드 게이트웨이가 모두 포함될 수 있습니다.

## IoT Hub는 어떻게 작동하나요?

Azure IoT Hub는 [서비스 지원 통신][lnk-service-assisted-pattern] 패턴을 구현하여 장치와 해당 응용 프로그램 백 엔드 간의 상호 작용을 중재합니다. 서비스 지원 통신의 목표는 IoT Hub 같은 제어 시스템과 신뢰할 수 없는 물리적 공간에 배포된 특수 용도 장치 간의 신뢰할 수 있는 양방향 통신 경로를 설정하는 것입니다. 패턴은 다음과 같은 원칙을 설정합니다.

- 보안이 다른 모든 기능보다 우선합니다.
- 장치는 요청하지 않은 네트워크 정보를 허용하지 않습니다. 장치에서 아웃바운드 전용 방식으로 모든 연결 및 경로를 설정합니다. 백 엔드에서 명령을 받는 장치의 경우 장치는 처리할 보류 중인 모든 명령의 확인에 대한 연결을 정기적으로 시작해야 합니다.
- 장치는 IoT Hub와 같은 잘 알려진 서비스에 피어링을 통해 연결하거나 경로를 설정해야 합니다.
- 장치와 서비스 또는 장치와 게이트웨이 간의 통신 경로는 응용 프로그램 프로토콜 계층에서 보안이 설정됩니다.
- 시스템 수준 권한 부여 및 인증은 장치 ID를 기반으로 하며 액세스 자격 증명 및 권한은 즉시 취소 가능합니다.
- 전원 또는 연결 문제로 산발적으로 연결된 장치에 대한 양방향 통신은 명령 및 장치 알림을 수신하기 위해 장치를 연결할 때까지 명령 및 장치 알림을 보유하여 용이해질 수 있습니다. IoT Hub는 보내는 명령에 대한 장치 특정 큐를 유지 관리합니다.
- 응용 프로그램 페이로드 데이터는 특정 서비스에 대한 게이트웨이를 통해 보호된 전송에 대해 개별적으로 보호됩니다.

모바일 업계에서는 [Windows 푸시 알림 서비스][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] 및 [Apple Push Notification Service][lnk-apple-push]와 같은 푸시 알림 서비스를 구현하기 위해 큰 규모로 서비스 보조 통신 패턴을 사용했습니다.

## 다음 단계

Azure IoT Hub를 사용하여 표준 기반 IoT 장치 관리를 통해 원격으로 장치를 관리, 구성, 업데이트할 수 있는 방법을 알아보려면 [Azure IoT Hub 장치 관리의 개요][lnk-device-management]를 참조하세요.

다양한 장치 하드웨어 플랫폼과 운영 체제에서 클라이언트 응용 프로그램을 구현하기 위해 IoT 장치 SDK를 제공할 수 있습니다. IoT 장치 SDK에는 IoT Hub로 원격 분석 전송 및 클라우드-장치 명령 수신을 용이하게 하는 라이브러리가 있습니다. SDK를 사용하면 다양한 네트워크 프로토콜 중에서 선택하여 IoT Hub와 통신할 수 있습니다. 자세한 내용은 [장치 SDK에 대한 정보][lnk-device-sdks]를 참조하세요.

일부 코드를 작성하고 몇 가지 샘플을 실행하기 시작하려면 [IoT Hub 시작][lnk-get-started] 자습서를 참조하세요.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "서비스 지원 통신, Clemens vasters의 블로그 게시물"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_1005_2016-->