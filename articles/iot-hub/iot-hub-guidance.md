<properties
 pageTitle="IoT Hub 솔루션 지침 | Microsoft Azure"
 description="Azure IoT Hub를 사용하여 IoT 솔루션을 개발하기 위한 게이트웨이, 장치 프로비전 및 인증에 대한 지침 항목입니다."
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
 ms.date="11/10/2015"
 ms.author="dobett"/>

# 솔루션 디자인

이 문서에서는 IoT 솔루션에서 다음과 같은 기능을 디자인하는 방법에 대한 지침을 제공합니다.

- 장치 프로비전
- 현장 게이트웨이
- 장치 인증

## 장치 프로비전

IoT 솔루션은 다음과 같은 개별 장치에 대한 데이터를 저장합니다.

- 장치 ID 및 인증 키
- 장치 하드웨어 유형 및 버전
- 장치 상태
- 소프트웨어 버전 및 기능
- 장치 명령 기록

지정된 IoT 솔루션에 저장된 장치 데이터는 해당 솔루션의 특정 요구 사항에 따라 달라지지만 적어도 장치 ID 및 인증 키는 저장해야 합니다. IoT Hub는 ID, 인증 키 및 상태 코드와 같은 각 장치에 대한 값을 저장할 수 있는 [ID 레지스트리][lnk-devguide-identityregistry]를 포함합니다. 솔루션은 테이블, BLOB 또는 DocumentDB와 같은 기타 Azure 서비스를 사용하여 추가 장치 데이터를 저장할 수 있습니다.

*장치 프로비전*은 솔루션 저장소에 초기 장치 데이터를 추가하는 프로세스입니다. 장치를 허브에 새로 연결하도록 하려면 새 장치 ID 및 키를 [IoT Hub ID 레지스트리][lnk-devguide-identityregistry]에 추가해야 합니다. 프로비전 프로세스의 일부로, 다른 솔루션 저장소에서 장치 특정 데이터를 초기화해야 할 수 있습니다.

[IoT Hub 장치 관리 지침][lnk-device-management] 문서에서는 장치 프로비전을 위한 일반적인 전략에 대해 설명합니다. [IoT Hub ID 레지스트리 API][lnk-devguide-identityregistry]를 통해 IoT Hub를 프로비전 프로세스에 통합할 수 있습니다.

## 현장 게이트웨이

IoT 솔루션에서 *현장 게이트웨이*는 장치와 IoT Hub 사이에 위치하며 일반적으로 장치 가까이에 위치합니다. 장치는 장치에 지원되는 프로토콜을 사용하여 현장 게이트웨이와 직접 통신하고 현장 게이트웨이는 IoT Hub에 지원되는 프로토콜을 사용하여 IoT Hub와 통신합니다. 현장 게이트웨이는 특수화된 독립 실행형 장치이거나 기존 하드웨어 부분에서 실행되는 소프트웨어일 수 있습니다.

현장 게이트웨이는 일반적으로 솔루션에서 액세스 및 정보 흐름을 관리하는 실제 역할을 수행하므로 단순한 트래픽 라우팅 장치(예: NAT 장치 또는 방화벽)와는 다릅니다. 예를 들어, 현장 게이트웨이는 다음과 같은 작업을 수행할 수 있습니다.

- 로컬 장치를 관리합니다. 예를 들어, 현장 게이트웨이는 이벤트 규칙 처리를 수행하고 특정 원격 분석 데이터에 대한 응답으로 장치에 명령을 보낼 수 있습니다.
- 데이터를 IoT Hub에 전달하기 전에 원격 분석 데이터를 필터링하거나 집계합니다. 이렇게 하면 IoT Hub로 전송되는 데이터 양이 줄어들어 잠재적으로 솔루션의 비용을 줄일 수 있습니다.
- 장치 프로비전을 지원합니다.
- 원격 분석 데이터를 솔루션 백 엔드에서 처리하기 쉽게 변환합니다.
- 장치가 IoT Hub에서 지원하는 전송 프로토콜을 사용하지 않는 경우에도 IoT Hub와 통신할 수 있도록 프로토콜 변환을 수행합니다.

> [AZURE.NOTE]일반적으로 현장 게이트웨이를 장치에 로컬로 배포하는 반면 일부 시나리오에서는 [프로토콜 게이트웨이][lnk-gateway]를 클라우드로 배포할 수 있습니다.

### 현장 게이트웨이 유형

현장 게이트웨이는 *투명* 또는 *불투명*할 수 있습니다.

| &nbsp; | 투명 | 불투명 |
|--------|-------------|--------|
| IoT Hub ID 레지스트리에 저장된 ID | 연결된 모든 장치 | 현장 게이트웨이의 ID만 |
| IoT Hub는 [장치 ID 스푸핑 방지][lnk-devguide-antispoofing]를 제공할 수 있음 | 예 | 아니요 |
| [제한 및 할당량][lnk-throttles-quotas] | 각 장치에 적용 | 현장 게이트웨이에 적용 |

### 기타 고려 사항

현장 게이트웨이를 구현하는 데 [Azure IoT 장치 SDK][lnk-device-sdks]를 사용할 수 있습니다. 일부 장치 SDK에서는 여러 장치에서 IoT Hub에 대한 동일한 연결로 통신을 멀티플렉싱하는 기능과 같은 현장 게이트웨이를 구현하는 데 도움이 되는 특정 기능을 제공합니다. [IoT Hub 개발자 가이드 - 통신 프로토콜 선택][lnk-devguide-protocol]에서 설명한 것처럼, 현장 게이트웨이에 대한 전송 프로토콜로 HTTP/1을 사용하지 않아야 합니다.

## 사용자 지정 장치 인증

IoT Hub [장치 ID 레지스트리][lnk-devguide-identityregistry]를 사용하여 장치별 보안 자격 증명 및 액세스 제어를 구성할 수 있습니다. 그러나 사용자 지정 장치 ID 레지스트리 및/또는 인증 체계에 이미 상당한 투자를 한 IoT 솔루션의 경우 *토큰 서비스*를 만들어 이 기존 인프라를 IoT Hub와 통합할 수 있습니다. 이러한 방식으로 솔루션에서 다른 IoT 기능을 사용할 수 있습니다.

토큰 서비스는 사용자 지정 클라우드 서비스로, **DeviceConnect** 권한으로 IoT Hub *공유 액세스 정책*을 사용하여 장치를 IoT Hub에 연결할 수 있는 *장치 범위* 토큰을 만듭니다.

  ![][img-tokenservice]

토큰 서비스 패턴의 주요 단계는 다음과 같습니다.

1. IoT Hub에 대한 **DeviceConnect** 권한으로 [IoT Hub 공유 액세스 정책][lnk-devguide-security]을 만듭니다. [Azure 포털][lnk-portal] 또는 프로그래밍 방식으로 이 정책을 만들 수 있습니다. 토큰 서비스는 이 정책을 사용하여 만들어지는 토큰을 서명합니다.
2. 장치에서 IoT Hub에 액세스해야 하는 경우 토큰 서비스에 서명된 토큰을 요청합니다. 장치는 사용자 지정 장치 ID 레지스트리/인증 체계로 인증하여 토큰 서비스가 토큰을 만드는 데 사용하는 장치 ID를 확인할 수 있습니다.
3. 토큰 서비스에서 [IoT Hub 개발자 가이드 - 보안][lnk-devguide-security]에 따라 `deviceId`를 인증할 장치로 함께 `resourceURI`로 `/devices/{deviceId}`를 사용하여 생성된 토큰을 반환합니다. 토큰 서비스는 공유 액세스 정책을 사용하여 토큰을 생성합니다.
4. 장치는 IoT Hub에서 직접 토큰을 사용합니다.

> [AZURE.NOTE].NET 클래스 [SharedAccessSignatureBuilder][lnk-dotnet-sas] 또는 Java 클래스 [IotHubServiceSasToken][lnk-java-sas]을 사용하여 토큰 서비스에서 토큰을 만듭니다.

토큰 서비스는 토큰 만료를 원하는 대로 설정할 수 있습니다. 토큰 만료 시, IoT Hub는 장치 연결을 끊고 장치는 토큰 서비스에서 새 토큰을 요청해야 합니다. 만료 시간이 짧으면 장치 및 토큰 서비스에 대한 부하가 증가합니다.

허브에 연결하는 장치의 경우, 장치가 연결에 장치 키가 아니라 토큰을 사용하더라도 IoT Hub 장치 ID 레지스트리에 장치를 추가해야 합니다. 따라서 장치가 토큰으로 인증하는 경우 [IoT Hub ID 레지스트리][lnk-devguide-identityregistry]에서 장치 ID를 활성화 또는 비활성화하여 장치별 액세스 제어를 계속 사용할 수 있습니다. 그러면 긴 만료 시간으로 토큰을 사용하는 위험이 완화됩니다.

### 사용자 지정 게이트웨이와 비교

IoT Hub에 사용자 지정 ID 레지스트리/인증 체계를 구현하는 데는 토큰 서비스 패턴이 권장되는 방법입니다. IoT Hub에서 대부분의 솔루션 트래픽을 계속 처리할 수 있기 때문입니다. 그러나 여기서는 사용자 지정 인증 체계가 프로토콜(예: [TLS PSK][lnk-tls-psk])과 복잡하게 얽혀 있어 서비스에서 모든 트래픽(*사용자 지정 게이트웨이*)을 처리해야 합니다. 자세한 내용은 [프로토콜 게이트웨이][lnk-gateway] 항목을 참조하세요.

## 다음 단계

Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [IoT Hub Azure란?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html

<!---HONumber=AcomDC_1203_2015-->