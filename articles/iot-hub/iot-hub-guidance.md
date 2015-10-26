<properties
 pageTitle="Azure IoT Hub 사용에 대한 지침 항목 | Microsoft Azure"
 description="Azure IoT Hub를 사용하는 솔루션 개발에 대한 지침 항목 모음입니다."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT Hub 지침

## 장치 프로비전

IoT 솔루션은 여러 다른 시스템 및 저장소에 장치 정보를 유지합니다. [IoT Hub의 ID 레지스트리][IoT Hub Developer Guide - identity registry]는 응용 프로그램 특정 장치 정보를 유지 관리하는 다른 저장소 중의 하나입니다. 이러한 모든 시스템에서 필요한 장치 정보를 만드는 과정을 *프로비전*이라고 합니다.

장치 프로비전을 위한 수많은 요구 사항과 전략이 있습니다.(자세한 내용은 [IoT Hub 장치 관리 지침] 참조) [IoT Hub ID 레지스트리][IoT Hub Developer Guide - identity registry]는 프로비전 프로세스에서 IoT Hub를 통합하는 데 필요한 API를 제공합니다.

## 현장 게이트웨이

현장 게이트웨이는 통신 인에이블러로 작동하고 잠재적으로 로컬 장치 제어 시스템 및 장치 데이터 처리 허브 역할을 하는 특수한 장치 어플라이언스 또는 범용 소프트웨어입니다. 현장 게이트웨이는 장치에 대한 로컬 처리 및 제어 기능을 수행할 수 있습니다. 한편으로는 장치 원격 분석을 필터 또는 집계할 수 있으므로 백 엔드에 전송되는 데이터 양을 줄여줍니다.

현장 게이트웨이의 범위에는 현장 게이트웨이 자체와 여기에 연결된 모든 장치가 포함됩니다. 이름에서 알 수 있듯이 현장 게이트웨이는 전용 데이터 처리 시설 외부에서 작동하며 일반적으로 바운딩된 위치입니다. 현장 게이트웨이는 액세스 및 정보 흐름을 관리하는 실제 역할을 한다는 점에서 단순한 트래픽 라우터와 다릅니다. 즉, 응용 프로그램 처리 엔터티 및 네트워크 연결이거나 세션 터미널입니다(예를 들어, 이 컨텍스트의 게이트웨이가 장치 프로비전, 데이터 변환, 프로토콜 변환 및 이벤트 규칙 처리를 지원할 수 있습니다). 한편, NAT 장치 또는 방화벽은 명시적인 연결 또는 세션 터미널이 아니고 이를 통과하는 연결 또는 세션을 라우팅(또는 거부)하므로 현장 게이트웨이로 한정되지 않습니다.

### 투명 및 불투명 현장 게이트웨이

장치 ID와 관련하여, 해당 범위의 기타 장치에 IoT Hub의 ID 레지스트리에 장치 ID 항목이 있는 경우 현장 게이트웨이를 *투명*하다고 합니다. IoT Hub의 ID 레지스트리에 있는 ID만 현장 게이트웨이의 ID인 경우 *불투명*하다고 합니다.

*불투명* 게이트웨이는 IoT Hub에서 [장치 ID 스푸핑 방지][IoT Hub Developer Guide - Anti-spoofing]를 제공하지 못하게 한다는 것에 유의해야 합니다. 또한 현장 게이트웨이 범위의 모든 장치는 IoT Hub에서 단일 장치로 표현되므로 단일 장치 기준의 한계 및 할당량이 적용됩니다.

### 기타 고려 사항

현장 게이트웨이를 구현할 때 [Azure IoT 장치 SDK][]를 사용할 수 있습니다. 일부 SDK는 동일한 연결에서 여러 장치 통신을 IoT Hub로 멀티플렉싱하는 기능과 같은 현장 게이트웨이 관련 기능을 제공합니다. [IoT Hub 개발자 가이드 - 통신 프로토콜 선택][]에서 설명한 것처럼, 현장 게이트웨이에 대한 전송 프로토콜로 HTTP/1을 사용하지 않아야 합니다.

## 사용자 지정 장치 인증 구성표/서비스 사용

IoT Hub를 사용하면 [장치 ID 레지스트리][IoT Hub Developer Guide - identity registry]를 사용하여 장치별 보안 자격 증명 및 액세스 제어를 구성할 수 있습니다.

사용자 지정 장치 ID 레지스트리 및/또는 인증 구성표에 이미 상당한 투자를 한 IoT 솔루션의 경우 IoT Hub에 대한 *토큰 서비스*를 만들어 기타 IoT Hub의 기능을 사용할 수 있습니다.

토큰 서비스는 자체 배포한 클라우드 서비스로, **DeviceConnect** 권한으로 IoT Hub *공유 액세스 정책*을 사용하여 *장치 범위* 토큰을 만듭니다.

  ![][img-tokenservice]

토큰 서비스 패턴의 주요 단계는 다음과 같습니다.

1. IoT Hub에 대한 **DeviceConnect** 권한으로 [IoT Hub 공유 액세스 정책][IoT Hub Developer Guide - Security]을 만듭니다. 이 정책은 토큰 서비스에서 토큰을 서명하는 데 사용됩니다.
2. 장치에서 IoT Hub에 액세스하려면 토큰 서비스에 서명된 토큰을 요청합니다. 장치에서 사용자 지정 장치 ID 레지스트리/인증 구성표를 사용할 수 있습니다.
3. 토큰 서비스에서 [IoT Hub 개발자 가이드 - 보안][]에 따라 `deviceId`를 인증할 장치로 함께 `resourceURI`로 `/devices/{deviceId}`를 사용하여 생성된 토큰을 반환합니다.
4. 장치는 IoT Hub에서 직접 토큰을 사용합니다.

토큰 서비스는 토큰 만료를 원하는 대로 설정할 수 있습니다. 만료 시 IoT Hub는 연결을 제공하고 장치는 토큰 서비스에 새 토큰을 요청해야 합니다. 분명히, 만료 시간이 짧으면 장치 및 토큰 서비스에 대한 부하가 증가합니다.

연결할 수 있는 장치에 대해 지정될 가치가 있으며 IoT Hub에서 장치 ID를 계속 만들어야 합니다. 즉, 장치가 토큰으로 인증되더라도 장치별 액세스 제어([IoT Hub 개발자 가이드 - ID 레지스트리][]에 따라 장치 ID 비활성화)가 계속 작동합니다. 이렇게 하면 장기간 토큰이 존재하는 것이 완화됩니다.

### 사용자 지정 게이트웨이와 비교

IoT Hub에 사용자 지정 ID 레지스트리/인증 구성표를 구현하는 데는 토큰 서비스 패턴이 권장되는 방법입니다. IoT Hub에서 대부분의 솔루션 트래픽을 처리할 수 있기 때문입니다. 그러나 여기서는 사용자 지정 인증 구성표가 프로토콜(예: [TLS PSK][])과 복잡하게 얽혀 있어 서비스에서 모든 트래픽(*사용자 지정 게이트웨이*)을 처리해야 합니다. 자세한 내용은 [프로토콜 게이트웨이][] 항목을 참조하세요.

## 다음 단계

Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [IoT Hub Azure란?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[IoT Hub 개발자 가이드 - ID 레지스트리]: iot-hub-devguide.md#identityregistry
[IoT Hub 장치 관리 지침]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Azure IoT 장치 SDK]: iot-hub-sdks-summary.md
[IoT Hub 개발자 가이드 - 통신 프로토콜 선택]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[IoT Hub 개발자 가이드 - 보안]: iot-hub-devguide.md#security
[TLS PSK]: https://tools.ietf.org/html/rfc4279
[프로토콜 게이트웨이]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Azure란?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO3-->