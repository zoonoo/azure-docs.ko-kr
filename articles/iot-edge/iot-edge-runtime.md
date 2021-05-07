---
title: 런타임에서 디바이스를 관리하는 방법 알아보기 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 런타임이 디바이스에서 모듈, 보안, 통신 및 보고를 관리하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496255"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge 런타임 및 해당 아키텍처 이해

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꾸는 프로그램 컬렉션입니다. 전체적으로 IoT Edge 런타임 구성 요소는 IoT Edge 디바이스를 사용하여 에지에서 실행될 코드를 받고 결과를 전달합니다.

IoT Edge 런타임은 IoT Edge 디바이스에서 다음 기능을 담당합니다.

* 디바이스에 워크로드를 설치하고 업데이트합니다.

* 디바이스에서 Azure IoT Edge 보안 표준을 유지합니다.

* [IoT Edge 모듈](iot-edge-modules.md)이 항상 실행되도록 해야 합니다.

* 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.

* 다운스트림 디바이스와 IoT Edge 디바이스 간의 통신을 관리합니다.

* IoT Edge 디바이스의 모듈 간 통신을 관리합니다.

* IoT Edge 디바이스와 클라우드 간의 통신을 관리합니다.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* IoT Edge 디바이스 간의 통신을 관리합니다.
::: moniker-end

![런타임은 인사이트 및 모듈 상태를 IoT Hub에 전달합니다.](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 런타임의 책임은 모듈 관리와 통신이라는 두 가지 범주로 나뉩니다. 이 두 역할은 IoT Edge 런타임의 일부인 두 가지 구성 요소를 통해 수행됩니다. ‘IoT Edge 에이전트’는 모듈을 배포 및 모니터링하며, ‘IoT Edge 허브’는 통신을 담당합니다. 

IoT Edge 에이전트와 IoT Edge 허브는 모두 IoT Edge 디바이스에서 실행되는 다른 모듈과 마찬가지로 모듈입니다. 때로는 ‘런타임 모듈’이라고도 합니다.

## <a name="iot-edge-agent"></a>IoT Edge 에이전트

IoT Edge 에이전트는 Azure IoT Edge 런타임을 구성하는 두 가지 모듈 중 하나입니다. 모듈을 인스턴스화하고, 모듈을 계속 실행하며, 모듈의 상태를 IoT Hub에 다시 보고합니다. 이 구성 데이터는 IoT Edge 에이전트 모듈 쌍의 속성으로 작성됩니다.

[IoT Edge 보안 디먼](iot-edge-security-manager.md)은 디바이스 시작 시 IoT Edge 에이전트를 시작합니다. 에이전트는 IoT Hub에서 모듈 쌍을 검색하고 배포 매니페스트를 검사합니다. 배포 매니페스트는 시작되어야 하는 모듈을 선언하는 JSON 파일입니다.

배포 매니페스트의 각 항목에는 모듈에 대한 특정 정보가 포함되며, 모듈의 수명 주기를 제어하기 위해 IoT Edge 에이전트에서 사용합니다. IoT Edge 에이전트가 모듈을 제어하는 데 사용하는 모든 속성에 대한 자세한 내용은 [IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)을 참조하세요.

IoT Edge 에이전트는 IoT Hub에 런타임 응답을 보냅니다. 가능한 응답 목록은 다음과 같습니다.
  
* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417 - 디바이스에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

배포 매니페스트 생성에 대한 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

### <a name="security"></a>보안

IoT Edge 에이전트는 IoT Edge 디바이스의 보안에서 중요한 역할을 합니다. 예를 들어 모듈의 이미지를 시작하기 전에 해당 이미지의 확인과 같은 작업을 수행합니다.

Azure IoT Edge 보안 프레임워크에 대한 자세한 정보는 [IoT Edge 보안 관리자](iot-edge-security-manager.md)를 참조하세요.

## <a name="iot-edge-hub"></a>IoT Edge 허브

IoT Edge 허브는 Azure IoT Edge 런타임을 구성하는 다른 모듈입니다. IoT Hub와 동일한 프로토콜 엔드포인트를 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다.

IoT Edge 허브는 로컬로 실행되는 IoT Hub의 전체 버전이 아닙니다. IoT Edge 허브는 일부 작업을 IoT Hub에 자동으로 위임합니다. 예를 들어 IoT Edge 허브는 첫 번째 연결에서 IoT Hub의 권한 부여 정보를 자동으로 다운로드하여 디바이스를 연결할 수 있도록 합니다. 첫 번째 연결이 설정되면 IoT Edge 허브에서 권한 부여 정보를 로컬로 캐시합니다. 해당 디바이스에서의 이후 연결은 권한 부여 정보를 클라우드에서 다시 다운로드할 필요 없이 인증됩니다.

### <a name="cloud-communication"></a>클라우드 통신

IoT Edge 허브는 IoT Edge 솔루션에서 사용하는 대역폭을 줄이기 위해 클라우드에 실제로 연결되는 수를 최적화합니다. IoT Edge 허브는 모듈 또는 다운스트림 디바이스에서 논리적 연결을 가져와서 클라우드에 대한 하나의 물리적 연결에 결합합니다. 이 프로세스의 세부 정보는 솔루션의 나머지 부분에 투명합니다. 클라이언트는 모두 동일한 연결을 통해 전송되는 경우에도 클라우드에 대한 자체 연결이 있다고 생각하면 됩니다. IoT Edge 허브는 AMQP 또는 MQTT 프로토콜을 사용하여 다운스트림 디바이스에서 사용하는 프로토콜과 별개로 클라우드와 업스트림 통신할 수 있습니다. 하지만 IoT Edge 허브는 현재 업스트림 프로토콜 및 해당 멀티플렉싱 기능으로 AMQP를 사용하여 논리적 연결을 단일 물리적 연결로 결합하는 것만 지원합니다. AMQP는 기본 업스트림 프로토콜입니다.

![IoT Edge 허브는 물리적 디바이스와 IoT Hub 간의 게이트웨이입니다.](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge 허브는 IoT Hub에 연결되어 있는지 여부를 결정할 수 있습니다. 연결이 끊어진 경우 IoT Edge 허브는 메시지 또는 쌍 업데이트를 로컬로 저장합니다. 연결이 다시 설정되면 모든 데이터가 동기화됩니다. 이 임시 캐시에 사용되는 위치는 IoT Edge 허브 모듈 쌍의 속성으로 결정됩니다. 캐시의 크기는 제한되지 않으며, 디바이스에 스토리지 용량이 있는 동안에는 증가합니다. 자세한 내용은 [오프라인 기능](offline-capabilities.md)을 참조하세요.

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>모듈 통신

IoT Edge 허브는 모듈 간 통신을 용이하게 합니다. IoT Edge 허브를 메시지 broker로 사용하면 모듈이 서로 독립적으로 유지됩니다. 모듈은 메시지를 수락하는 입력과 메시지를 쓰는 출력을 지정하기만 하면 됩니다. 솔루션 개발자는 해당 입력과 출력을 함께 연결하여 모듈에서 해당 솔루션과 관련된 순서로 데이터를 처리하도록 할 수 있습니다.

![IoT Edge 허브는 모듈 간 통신을 용이하게 합니다](./media/iot-edge-runtime/module-endpoints.png)

IoT Edge 허브에 데이터를 보내려면 모듈에서 SendEventAsync 메서드를 호출합니다. 첫 번째 인수는 메시지를 보낼 출력을 지정합니다. 다음 의사 코드는 메시지를 **output1** 에 보냅니다.

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

메시지를 수신하려면 특정 입력에 들어오는 메시지를 처리하는 콜백을 등록합니다. 다음 의사 코드는 **input1** 에서 받은 모든 메시지를 처리하는 데 사용할 messageProcessor 함수를 등록합니다.

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

ModuleClient 클래스 및 해당 통신 방법에 대한 자세한 내용은 원하는 SDK 언어([C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) 또는 [Node.js](/javascript/api/azure-iot-device/moduleclient))에 대한 API 참조를 확인하세요.

솔루션 개발자는 IoT Edge 허브에서 모듈 간에 메시지를 전달하는 방식을 결정하는 규칙을 지정합니다. 라우팅 규칙은 클라우드에서 정의되며 모듈 쌍의 IoT Edge 허브로 푸시 다운됩니다. IoT Hub 경로에 대한 동일한 구문이 Azure IoT Edge의 모듈 간 경로를 정의하는 데 사용됩니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로를 설정하는 방법 알아보기](module-composition.md)을 참조하세요.

![모듈 간 경로는 IoT Edge 허브를 통과합니다.](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>로컬 통신

IoT Edge 허브는 로컬 통신을 용이하게 합니다. 디바이스 및 모듈을 서로 독립적으로 유지하기 위해 메시지를 중개하여 디바이스와 모듈, 모듈과 모듈, 디바이스와 디바이스 간의 통신을 지원합니다.

>[!NOTE]
> MQTT Broker 기능은 IoT Edge 버전 1.2에서 퍼블릭 미리 보기로 제공됩니다. 명시적으로 사용하도록 설정해야 합니다.

IoT Edge 허브는 두 가지 중개 메커니즘을 지원합니다.

1. [IoT Hub에서 지원하는 메시지 라우팅 기능](../iot-hub/iot-hub-devguide-messages-d2c.md)과
2. [MQTT 표준 v3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)을 충족하는 범용 MQTT Broker입니다.

#### <a name="using-routing"></a>라우팅 사용

첫 번째 중개 메커니즘은 IoT Hub와 동일한 라우팅 기능을 활용하여 디바이스 또는 모듈 간에 메시지를 전달하는 방법을 지정합니다. 첫 번째 디바이스 또는 모듈은 메시지를 수락하는 입력과 메시지를 작성하는 출력을 지정합니다. 그런 다음 솔루션 개발자는 잠재적인 필터를 사용하여 소스(예: 출력)와 대상(예: 입력) 간에 메시지를 라우팅할 수 있습니다.

![모듈 간 경로는 IoT Edge 허브를 통과합니다.](./media/iot-edge-runtime/module-endpoints-routing.png)

라우팅은 AMQP 또는 MQTT 프로토콜을 통해 Azure IoT 디바이스 SDK로 빌드된 디바이스 또는 모듈에서 사용할 수 있습니다. 모든 메시징 IoT Hub 기본 형식(예: 원격 분석, 직접 메서드, C2D, 트윈)은 지원되지만 사용자 정의 항목을 통한 통신은 지원되지 않습니다.

경로에 대한 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

#### <a name="using-the-mqtt-broker"></a>MQTT Broker 사용

두 번째 중개 메커니즘은 표준 MQTT Broker를 기반으로 합니다. MQTT는 리소스 제한 디바이스에서 최적의 성능을 보장하는 경량 메시지 전송 프로토콜이며 널리 사용되는 게시 및 구독 표준입니다. 디바이스 또는 모듈은 항목을 구독하여 다른 디바이스 또는 모듈에서 게시한 메시지를 수신합니다. IoT Edge 허브는 [MQTT 버전 3.1.1의 사양](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)을 따르는 자체 MQTT Broker를 구현합니다.

MQTT Broker를 사용하면 라우팅에 비해 로컬 브로드캐스팅 및 지점 간 통신이라는 두 가지 추가 통신 패턴을 사용할 수 있습니다. 로컬 브로드캐스팅은 하나 이상의 디바이스 또는 모듈에서 여러 개의 다른 디바이스 또는 모듈에 로컬로 경고해야 하는 경우에 유용합니다. 지점 간 통신을 사용하면 두 개의 IoT Edge 디바이스 또는 두 개의 IoT 디바이스가 클라우드로 왕복하지 않고 로컬에서 통신할 수 있습니다.

![IoT Edge 허브를 사용하는 로컬 게시 및 구독](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

MQTT Broker는 MQTT 프로토콜을 통해 통신하는 Azure IoT 디바이스 SDK 또는 범용 MQTT 클라이언트를 사용하여 빌드된 디바이스 또는 모듈에서 사용할 수 있습니다. C2D를 제외한 모든 메시징 IoT Hub 기본 형식(예: 원격 분석, 직접 메서드, 트윈)이 지원됩니다. IoT Hub 기본 형식에서 사용하는 IoT Hub 특수 항목이 지원되며 사용자 정의 항목입니다.
이 항목은 IoT Hub 특수 항목이거나 사용자 정의 항목일 수 있습니다.

라우팅 메커니즘과 달리 메시지 순서는 최선의 방법일 뿐이며 보장되지 않습니다. Broker에서는 메시지 필터링을 지원하지 않습니다. 그러나 이러한 기능이 없기 때문에 MQTT Broker는 라우팅보다 빠르게 작동합니다.

MQTT Broker에 대한 자세한 내용은 [IoT Edge를 사용하는 게시 및 구독](how-to-publish-subscribe.md)을 참조하세요.

#### <a name="comparison-between-brokering-mechanisms"></a>중개 메커니즘 비교

각 중개 메커니즘과 함께 사용할 수 있는 기능은 다음과 같습니다.

|기능  | 라우팅  | MQTT Broker  |
|---------|---------|---------|
|D2C 원격 분석    |     &#10004;    |         |
|로컬 원격 분석     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|트윈     |    &#10004;     |    &#10004;     |
|디바이스에 대한 C2D     |   &#10004;      |         |
|순서 지정     |    &#10004;     |         |
|필터링     |     &#10004;    |         |
|사용자 정의 항목     |         |    &#10004;     |
|디바이스 간     |         |    &#10004;     |
|로컬 브로드캐스팅     |         |    &#10004;     |
|성능     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>IoT Edge 허브에 연결하기

IoT Edge 허브는 MQTT 프로토콜 또는 AMQP 프로토콜을 통해 디바이스 또는 모듈 클라이언트에서 연결을 허용합니다.

>[!NOTE]
> IoT Edge 허브는 MQTT 또는 AMQP를 사용하여 연결되는 클라이언트를 지원합니다. HTTP를 사용하는 클라이언트는 지원하지 않습니다.

클라이언트에서 IoT Edge 허브에 연결하면 다음 작업이 수행됩니다.

1. TLS(전송 계층 보안)를 사용하는 경우(권장 사항), TLS 채널이 클라이언트와 IoT Edge 허브 간의 암호화된 통신을 설정하도록 빌드됩니다.
2. 인증 정보가 클라이언트에서 IoT Edge 허브로 전송되어 자신을 식별합니다.
3. IoT Edge 허브는 권한 부여 정책에 따라 연결을 승인하거나 거부합니다.

#### <a name="secure-connections-tls"></a>보안 연결(TLS)

기본적으로 IoT Edge 허브는 TLS(전송 계층 보안)로 보호되는 연결(예: 제3자가 암호 해독할 수 없는 암호화된 연결)만 허용합니다.

클라이언트가 포트 8883(MQTTS) 또는 5671(AMQPS)에서 IoT Edge 허브에 연결하는 경우 TLS 채널을 빌드해야 합니다. TLS 핸드셰이크 중에 IoT Edge 허브가 클라이언트의 유효성을 검사하는 데 필요한 인증서 체인을 보냅니다. 인증서 체인의 유효성을 검사하려면 IoT Edge 허브의 루트 인증서를 클라이언트에서 신뢰할 수 있는 인증서로 설치해야 합니다. 루트 인증서를 신뢰할 수 없는 경우 클라이언트 라이브러리는 인증서 확인 오류가 발생한 IoT Edge 허브에서 거부됩니다.

디바이스 클라이언트에서 Broker의 해당 루트 인증서를 설치하기 위해 따라야 하는 단계는 [투명 게이트웨이](how-to-create-transparent-gateway.md) 및 [다운스트림 디바이스 준비](how-to-connect-downstream-device.md#prepare-a-downstream-device) 문서에 설명되어 있습니다. 모듈은 IoT Edge 디먼 API를 활용하여 IoT Edge 허브와 동일한 루트 인증서를 사용할 수 있습니다.

#### <a name="authentication"></a>인증

IoT Edge 허브는 IoT Hub ID를 보유한 디바이스 또는 모듈에서의 연결만 허용합니다. 예를 들면 IoT Hub에 등록되어 있으며 IoT Hub에서 지원하는 다음 세 가지 클라이언트 인증 방법 중 하나를 사용하여 ID 증명을 제공할 수 있습니다. [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [X.509 자체 서명 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [X.509 CA 서명 인증](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)  해당 IoT Hub ID는 IoT Edge 허브에서 로컬로 확인할 수 있기 때문에 오프라인 상태인 동안에도 연결할 수 있습니다.

참고:

* IoT Edge 모듈은 현재 대칭 키 인증만 지원합니다.
* 로컬 사용자 이름 및 암호만 사용하는 MQTT 클라이언트는 IoT Edge 허브 MQTT Broker에서 허용되지 않으며 IoT Hub ID를 사용해야 합니다.

#### <a name="authorization"></a>권한 부여

인증된 IoT Edge 허브는 다음 두 가지 방법으로 클라이언트 연결에 권한을 부여합니다.

* 클라이언트가 IoT Hub에 정의된 신뢰할 수 있는 클라이언트 집합에 속하는지 확인합니다. IoT Hub에서 부모/자식 또는 디바이스/모듈 관계를 설정하여 신뢰할 수 있는 클라이언트 집합을 지정합니다. IoT Edge에서 모듈을 만들면 해당 모듈과 해당 IoT Edge 디바이스 간에 트러스트 관계가 자동으로 설정됩니다. 이는 라우팅 중개 메커니즘에서 지원되는 유일한 권한 부여 모델입니다.

* 권한 부여 정책을 설정합니다. 해당 권한 부여 정책은 IoT Edge 허브의 리소스에 액세스할 수 있는 모든 권한이 부여된 클라이언트 ID를 나열하는 문서입니다. 이는 IoT Edge 허브 MQTT Broker에서 사용되는 기본 권한 부여 모델이지만 IoT Hub 항목의 경우에는 MQTT Broker에서 부모/자식 및 디바이스/모듈 관계를 파악할 수도 있습니다.

### <a name="remote-configuration"></a>원격 구성

IoT Edge 허브는 완전히 클라우드로 제어됩니다. [모듈 쌍](iot-edge-modules.md#module-twins)을 통해 IoT Hub에서 구성을 가져옵니다. 다음을 포함합니다.

* 경로 구성
* 네임스페이스 수준에서 구성하는 권한 부여 정책도
* MQTT 브리지 구성

또한 [IoT Edge 허브의 환경 변수](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)를 설정하여 몇 가지 구성 작업을 수행할 수 있습니다.
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>런타임 품질 원격 분석

IoT Edge는 호스트 런타임 및 시스템 모듈에서 익명의 원격 분석을 수집하여 제품 품질을 개선합니다. 이 정보를 런타임 품질 원격 분석이라고 합니다. 수집된 원격 분석은 IoT Edge 에이전트에서 IoT Hub로, 디바이스와 클라우드 간의 메시지 형태로 주기적으로 전송됩니다. 해당 메시지는 고객의 일반 원격 분석에 표시되지 않으며 메시지 할당량을 사용하지 않습니다.

IoT Edge 에이전트 및 허브는 디바이스의 성능을 파악할 수 있도록 수집 가능한 메트릭을 생성합니다. 해당 메트릭의 하위 집합은 런타임 품질 원격 분석의 일부로 IoT Edge 에이전트가 수집합니다. 런타임 품질 원격 분석을 위해 수집된 메트릭은 `ms_telemetry` 태그로 레이블이 지정됩니다. 사용 가능한 모든 메트릭에 대한 자세한 내용은 [기본 제공 메트릭 액세스](how-to-access-built-in-metrics.md)를 참조하세요.

디바이스 및 모듈 이름과 같이 개인이나 조직이 식별할 수 있는 정보는 런타임 품질 원격 분석의 익명 특성을 보장하기 위해 업로드 전에 제거됩니다.

IoT Edge 에이전트는 1시간마다 원격 분석을 수집하고 24시간마다 메시지 하나를 IoT Hub로 보냅니다.

디바이스에서 런타임 원격 분석을 보내는 것을 옵트아웃하려는 경우 다음 두 가지 방법을 사용할 수 있습니다.

* `SendRuntimeQualityTelemetry`환경 변수를 **edgeAgent** 에 대해 `false`로 설정하거나
* 배포하는 동안 Azure Portal에서 옵션의 선택을 취소합니다.

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge 모듈 이해](iot-edge-modules.md)
* [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)
* [IoT Edge를 사용하여 게시 및 구독하는 방법 알아보기](how-to-publish-subscribe.md)
* [IoT Edge 런타임 메트릭에 대해 알아보기](how-to-access-built-in-metrics.md)
