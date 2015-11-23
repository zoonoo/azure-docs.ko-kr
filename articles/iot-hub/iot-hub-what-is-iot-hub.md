<properties
 pageTitle="Azure IoT Hub 개요 | Microsoft Azure"
 description="장치 연결, 통신 패턴 및 서비스 보조 통신 패턴을 포함한 Azure IoT Hub 서비스에 대한 개요"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/09/2015"
 ms.author="dobett"/>

# Azure IoT Hub란?

Azure IoT Hub를 시작합니다. 이 문서에서는 Azure IoT Hub의 개요를 제공하고 IoT 솔루션을 구현할 때 이 서비스를 사용하는 이유에 대해 설명합니다.

Azure IoT Hub는 수백만의 IoT 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. Azure IoT Hub는 규모에 따른 안정적인 장치-클라우드 및 클라우드-장치 메시징을 제공하고 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 보안 통신을 사용하며 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

![클라우드 게이트웨이로 IoT Hub?][img-architecture]

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

-   **장치별 인증 및 보안 연결**. IoT Hub에 연결할 수 있도록 각 장치를 고유한 보안 키로 프로비전할 수 있습니다. 솔루션 백 엔드는 개별 장치를 허용 목록 및 블랙 리스트에 추가하여 장치 액세스를 완전히 제어할 수 있습니다.

-   **광범위한 장치 라이브러리 집합**. Azure IoT 장치 SDK는 사용 가능하며 다양한 언어 및 플랫폼(여러 Linux 배포판, Windows 및 RTOS에 대한 C)에 대해 지원됩니다. 또한 Azure IoT 장치 SDK에서는 C#, Java 및 JavaScript와 같은 관리된 언어를 지원합니다.

-   **IoT 프로토콜 및 확장성**. 솔루션이 장치 라이브러리를 사용할 수 없는 경우 IoT Hub는 장치가 기본적으로 HTTP 1.1 및 AMQP 1.0 프로토콜을 사용할 수 있도록 허용하는 공용 프로토콜을 노출합니다. IoT Hub를 확장하여 [Azure IoT 프로토콜 게이트웨이][protocol-gateway] 공개 소스 구성 요소와 MQTT v3.1.1에 대한 지원을 제공할 수 있습니다. 클라우드에서 또는 온-프레미스에서 Azure IoT 프로토콜 게이트웨이를 실행하고 사용자 지정 프로토콜을 지원하도록 확장할 수 있습니다.

-   **규모**. Azure IoT Hub는 동시에 연결된 수백만 대의 장치 및 수백만 개의 초당 이벤트로 크기 조정합니다.

이러한 이점은 여러 통신 패턴에 대해 일반적으로 적용됩니다. 현재 IoT Hub를 통해 다음과 같은 특정 통신 패턴을 구현할 수 있습니다.

-   **이벤트 기반 장치-클라우드 수집.** IoT Hub는 장치에서 초당 수백만 개의 이벤트를 안정적으로 수신하고 이벤트 프로세서 엔진을 사용하여 실행 부하 과다 경로에서 이를 처리하거나 분석을 위해 콜드 경로에 저장할 수 있습니다. IoT Hub는 안정적인 처리를 보장하고 부하에서 최대치를 흡수하도록 최대 7일 동안 이벤트 데이터를 유지합니다.

-   **신뢰할 수 있는 클라우드-장치 메시징(또는 *명령*).** 솔루션 백 엔드는 IoT Hub를 사용하여 적어도 한 번 배달 보증으로 메시지를 개별 장치에 보낼 수 있습니다. 각 메시지는 개별 TTL(Time-to-Live) 설정이 있으며 백 엔드는 클라우드-장치 메시지의 수명 주기에 대해 전체 가시성을 확인하도록 배달 및 만료 수신 확인을 모두 요청할 수 있습니다. 이렇게 하면 장치에서 실행하는 작업을 포함하는 비즈니스 논리를 구현할 수 있습니다.

일관된 장치 ID 관리, 연결 모니터링 및 크기 조정과 같은 IoT Hub의 IoT 특정 기능을 활용하여 파일 업로드 및 다운로드와 같은 다른 일반적인 패턴도 구현할 수 있습니다.

## IoT Hub는 어떻게 작동하나요?

Azure IoT Hub는 [서비스 지원 통신][lnk-service-assisted-pattern] 패턴을 구현하여 장치와 해당 응용 프로그램 백 엔드 간의 상호 작용을 중재합니다. 서비스 지원 통신의 목표는 제어 시스템(예: IoT Hub)과 신뢰할 수 없는 물리적 공간에 배포된 특수 용도 장치 간의 신뢰할 수 있는 양방향 통신 경로를 설정하는 것입니다. 패턴은 다음과 같은 원칙을 설정합니다.

- 보안이 다른 모든 기능보다 우선합니다.
- 장치는 요청하지 않은 네트워크 정보를 허용하지 않습니다. 장치에서 아웃바운드 전용 방식으로 모든 연결 및 경로를 설정합니다. 백 엔드에서 명령을 받는 장치의 경우 장치는 처리할 보류 중인 모든 명령의 확인에 대한 연결을 정기적으로 시작해야 합니다.
- 장치는 IoT Hub와 같은 잘 알려진 서비스에 피어링을 통해 연결하거나 경로를 설정해야 합니다.
- 장치와 서비스 또는 장치와 게이트웨이 간의 통신 경로는 응용 프로그램 프로토콜 계층에서 보안이 설정됩니다.
- 시스템 수준 권한 부여 및 인증은 장치 ID를 기반으로 하며 액세스 자격 증명 및 권한은 즉시 취소 가능합니다.
- 전원 또는 연결 문제로 산발적으로 연결된 장치에 대한 양방향 통신은 명령 및 장치 알림을 수신하기 위해 장치를 연결할 때까지 명령 및 장치 알림을 보유하여 용이해 질 수 있습니다. IoT Hub는 보내는 명령에 대한 장치 특정 큐를 유지 관리합니다.
- 응용 프로그램 페이로드 데이터는 특정 서비스에 대한 게이트웨이를 통해 보호된 전송에 대해 개별적으로 보호됩니다.

서비스 보조 통신 패턴은 [Windows 알림 서비스](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), [Google 클라우드 메시징](https://developers.google.com/cloud-messaging/) 및 [Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)와 같은 푸시 알림 서비스를 구현하기 위해 큰 규모로 모바일 업계에서 성공적으로 사용되었습니다.

## 다음 단계

Azure IoT Hub에 대해 더 알아보려면 다음 링크를 참조하세요.

* [IoT Hub 시작]
* [장치 연결]
* [장치-클라우드 메시지 처리]

[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[장치 연결]: https://azure.microsoft.com/develop/iot/
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "서비스 지원 통신, Clemens vasters의 블로그 게시물"

<!---HONumber=Nov15_HO3-->