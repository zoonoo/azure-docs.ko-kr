---
title: Azure IoT Edge 인증서 이해 | Microsoft Docs
description: Azure IoT Edge 인증서 및 사용 방법에 대해 알아봅니다.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ff85f2c9ef708c7cbac9be4933541f063a6c95b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568593"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Azure IoT Edge 인증서 사용 현황 세부 정보

IoT Edge 인증서는 모듈 및 다운스트림 IoT 장치에서 연결되어 있는 [Edge Hub](iot-edge-runtime.md#iot-edge-hub) 런타임 모듈의 ID 및 적법성을 확인하는 데 사용됩니다. 이러한 유효성 검사를 통해 런타임, 모듈 및 IoT 장치 간의 TLS(전송 계층 보안) 보안 연결을 사용하도록 설정합니다. IoT Hub 자체와 마찬가지로 IoT Edge에는 IoT 다운스트림(또는 리프) 장치 및 IoT Edge 모듈의 보안 및 암호화된 연결이 필요합니다. 보안 TLS 연결을 설정하기 위해 Edge Hub 모듈은 해당 ID를 확인할 수 있도록 클라이언트를 연결하는 서버 인증서 체인을 표시합니다.

IoT Edge의 인증서 사용에 관한 혼란을 없애기 위해 이 문서에서는 IoT Edge 인증서가 개발 및 테스트 시나리오뿐만 아니라 확장 시나리오에서도 작동하는 방식을 설명합니다. 스크립트는 다르지만(Powershell 대 bash), 개념은 Linux와 Windows 간에 동일합니다.

## <a name="iot-edge-certificates"></a>IoT Edge 인증서

대부분의 경우에서 제조업체는 Edge 장치의 최종 사용자로부터 독립적인 엔터티입니다. 제조업체와 운영자 간의 유일한 관계가 상업적으로 사용할 수 있는 Edge 장치의 구입인 경우도 있습니다. 다른 경우에 제조업체가 운영자를 대신하여 Edge 장치를 빌드하는 계약을 체결하기도 합니다. IoT Edge 인증서 디자인은 두 시나리오를 모두 고려하고자 합니다.

다음 그림은 IoT Edge의 인증서 사용을 보여줍니다. 루트 CA 인증서와 장치 CA 인증서 사이에 중간 서명 인증서는 하나도 없을 수도 있고, 하나일 수도 있고, 여러 개일 수도 있습니다. 관련된 엔터티 수에 따라 한 사례를 살펴보겠습니다.

![일반적인 인증서 관계의 다이어그램](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>인증 기관

인증 기관 또는 줄여서 ‘CA’는 디지털 인증서를 발급하는 엔터티입니다. 인증 기관은 소유자 및 와 인증서의 받는 사람 간에 신뢰할 수 있는 타사 역할을 합니다. 디지털 인증서는 인증서의 받는 사람에 대한 공개 키의 소유권을 인정합니다. 인증서 신뢰 체인은 초기에 루트 인증서가 발급되면서 작동합니다. 이는 기관에서 발급되는 모든 인증서에서 신뢰의 기반이 됩니다. 그 후에, 소유자는 루트 인증서를 사용하여 추가 중간 인증서(‘리프’ 인증서)를 발급할 수 있습니다.

### <a name="root-ca-certificate"></a>루트 CA 인증서

루트 CA 인증서는 전체 프로세스에서 신뢰의 기반입니다. 프로덕션 시나리오에서 이는 일반적으로 Baltimore, Verisign, DigiCert 등과 같이 신뢰할 수 있는 상용 인증 기관에서 구입한 CA 인증서입니다. Edge 장치에 연결하는 장치를 완전히 제어할 수 있어야, 회사 수준 인증 기관을 사용할 수 있습니다. 어떤 경우에도 Edge Hub의 전체 인증서 체인은 루트 인증서까지 이어져야 하므로 리프 IoT 장치는 루트 인증서를 신뢰해야 합니다. 루트 CA 인증서를 신뢰할 수 있는 루트 인증 기관 저장소에 저장하거나, IoT 클라이언트가 IoT Edge에 연결할 때 루트 CA 인증서 세부 사항을 코드에 제공할 수 있습니다.

### <a name="intermediate-certificates"></a>중간 인증서

보안 장치를 만들기 위한 일반적인 제조 프로세스에서 주로 유출 또는 노출의 위험으로 인해 루트 CA 인증서가 직접 사용되는 경우는 거의 없습니다. 일반적으로 하나 이상의 중간 CA 인증서가 생성되고 루트 CA 인증서에 의해 디지털로 서명됩니다. 중간 인증서가 하나만 있을 수도 있고, 아니면 이러한 중간 인증서의 체인이 있을 수도 있습니다. 중간 인증서의 체인이 필요한 시나리오는 다음과 같습니다.

* 제조업체 내 부서의 계층 구조.

* 장치의 프로덕션에 순차적으로 참여하는 여러 회사.

* 제조업체가 해당 고객을 대신하여 만든 장치를 서명하도록 루트 CA를 구매하고, 서명 인증서를 파생하는 고객.

어떤 경우든 제조업체는 이 체인의 마지막에 중간 CA 인증서를 사용하여 최종 장치에 배치된 장치 CA 인증서에 서명합니다. 일반적으로 이러한 중간 인증서는 제조 공장에서 엄격히 보호됩니다. 물리적 및 전자적으로 해당 사용에 대해 엄격한 프로세스를 거칩니다.

### <a name="device-ca-certificate"></a>장치 CA 인증서

장치 CA 인증서는 프로세스에서 최종 중간 CA 인증서로 생성 및 서명됩니다. 이 인증서는 Edge 장치 자체에 설치되며 HSM(하드웨어 보안 모듈)과 같은 보안 저장소에서 더 선호됩니다. 또한 장치 CA 인증서는 IoT Edge 장치를 고유하게 식별합니다. IoT Edge의 경우 장치 CA 인증서가 다른 인증서를 발급할 수 있습니다. 예를 들어 장치 CA 인증서는 [Azure IoT Device Provisioning Service](..\iot-dps\about-iot-dps.md)에 대해 장치를 인증하는 데 사용되는 리프 장치 인증서를 발급합니다.

### <a name="iot-edge-workload-ca"></a>IoT Edge 워크로드 CA

프로세스에서 첫 번째 “운영자”측인 이 인증서는 IoT Edge를 처음 시작할 때 [IoT Edge 보안 관리자](iot-edge-security-manager.md)에서 생성됩니다. 이 인증서는 “장치 CA 인증서”에서 생성되며 서명됩니다. 또 다른 중간 서명 인증서인 이 인증서는 IoT Edge 런타임에서 사용되는 다른 인증서를 생성 및 서명하는 데 사용됩니다. 현재 이는 주로 다음 섹션에서 논의되는 Edge Hub 서버 인증서이지만, 앞으로 IoT Edge 구성 요소를 인증하기 위해 다른 인증서를 포함할 수 있습니다.

### <a name="edge-hub-server-certificate"></a>Edge Hub 서버 인증서

Edge Hub 서버 인증서는 IoT Edge에 요구되는 TLS 연결을 설정하는 동안 ID를 확인하는 리프 장치와 모듈에 표시되는 실제 인증서입니다. 이 인증서는 리프 IoT 장치가 신뢰해야 하는 루트 CA 인증서를 생성하는 데 사용되는 서명 인증서의 전체 체인을 표시합니다. IoT Edge 보안 관리자에서 생성되는 경우 이 Edge Hub 인증서의 CN(일반 이름)은 config.yaml 파일의 ‘hostname’ 속성에서 제공하는 이름으로 소문자로 변환된 후 설정됩니다. 이는 IoT Edge의 혼란에 관한 일반적인 원인입니다.

## <a name="production-implications"></a>프로덕션 영향

“IoT Edge에 ‘워크로드 CA’ 추가 인증서는 왜 필요한가요? 장치 CA 인증서를 사용하여 Edge Hub 서버 인증서를 직접 생성할 수는 없나요?”란 질문은 타당할 수 있습니다. 기술적으로 말하자면, 가능합니다. 그러나 이 “워크로드” 중간 인증서의 목적은 장치 제조업체와 장치 운영자 간에 문제를 분리하는 것입니다. IoT Edge 장치가 한 고객에게서 다른 고객에게 팔리거나 전송되는 시나리오를 상상해보세요. 제조업체에서 제공한 변경이 불가능한 장치 CA 인증서를 원할 가능성이 높습니다. 그러나 장치 작업별 “워크로드” 인증서는 새 배포를 위해 초기화되고 다시 생성되어야 합니다.

제조업체 및 운영자 프로세스는 구분되므로 프로덕션 장치에 대한 IoT Edge 롤아웃과 관련한 몇 가지 영향을 고려해야 합니다.

* 모든 인증서 기반 프로세스에서는 IoT Edge 장치를 롤아웃하는 전체 프로세스 동안 루트 CA 인증서 및 중간 CA 인증서를 보호하고 모니터링해야 합니다. IoT Edge 장치 제조업체는 중간 인증서를 적절하게 저장하고 사용할 수 있도록 강력한 프로세스를 갖추어야 합니다. 또한 장치 CA 인증서는 장치 자체, 특히 하드웨어 보안 모듈에 최대한 안전하게 저장해야 합니다.

* Edge Hub 서버 인증서는 Edge Hub에서 연결된 클라이언트 장치 및 모듈에 표시되므로 장치 CA 인증서의 CN은 IoT Edge 장치에서 config.yaml에 사용할 “hostname”과 **반드시 달라야** 합니다. 예를 들어 연결 문자열의 GatewayHostName 매개 변수 또는 MQTT의 CONNECT 명령을 통해 IoT Edge에 연결할 클라이언트에서 사용하는 이름은 장치 CA 인증서에 사용되는 일반 이름과 동일할 **수는 없습니다**. Edge Hub가 클라이언트에서 검증하도록 해당 전체 인증서 체인을 표시하기 때문입니다. Edge Hub 서버 인증서와 장치 CA 인증서의 CN이 모두 동일한 경우 유효성 확인 반복 상태가 되며 인증서는 무효화됩니다.

* 최종 IoT Edge 인증서를 생성하기 위해 IoT Edge 보안 디먼에서 장치 CA 인증서를 사용하므로, 자체적으로 서명 인증서가 되어야 합니다. 즉, 인증서 서명 기능이 있어야 합니다. 서명 CA 인증서로 만들기 위해 기본적으로 “V3 기본 제약 조건 CA:True”를 장치 CA 인증서에 적용하면 필수 키 용도 속성이 자동으로 설정됩니다.

>[!Tip]
> 이미 IoT Edge를 “편리한 스크립트”를 사용한 개발/테스트 시나리오에서 투명 게이트웨이로 설정하고(다음 섹션 참조), config.yaml의 호스트 이름에 대해 수행한 것처럼 장치 CA 인증서를 만들 때 동일한 호스트 이름을 사용한 경우에는 왜 작동하는지 궁금할 것입니다. 개발자 환경을 간소화하기 위한 노력으로, 편리한 스크립트는 사용자가 스크립트에 전달하는 이름의 마지막에 “ca”를 추가합니다. 따라서 예를 들어, 스크립트의 장치 이름과 config.yaml의 호스트 이름 모두에 “mygateway”를 사용한 경우 전자는 장치 CA 인증서에 대한 CN으로 사용되기 전에 mygateway.ca로 바뀝니다.

## <a name="devtest-implications"></a>개발/테스트 영향

개발 및 테스트 시나리오를 간소화하기 위해 Microsoft는 투명 게이트웨이 시나리오의 IoT Edge에 적합한 비-프로덕션 인증서를 생성하기 위한 [편리한 스크립트](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) 집합을 제공합니다. 스크립트 작동 방식의 예제는 [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)을 참조하세요.

이러한 스크립트는 이 문서에서 설명한 인증서 체인 구조체를 따르는 인증서를 생성합니다. 다음 명령은 “루트 CA 인증서” 및 단일 “중간 CA 인증서”를 생성합니다.

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

또한 이러한 명령은 “장치 CA 인증서”를 생성합니다.

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* 해당 스크립트로 전달되는 **\<gateway device name\>** 은 config.yaml에서 “hostname” 매개 변수와 **달라야 합니다**. 언급했듯이 사용자가 두 위치 모두에 동일한 이름을 사용하여 Edge를 설정하는 경우 이름 충돌을 방지하기 위해 스크립트는 “.ca” 문자열을 **\<gateway device name\>** 에 추가하여 문제를 방지하는 데 도움이 됩니다. 하지만 동일한 이름을 사용하지 않는 것이 바람직합니다.

>[!Tip]
> 장치 IoT “리프” 장치 및 IoT Edge를 통해 IoT 장치 SDK를 사용하는 응용 프로그램에 연결하려면 장치의 연결 문자열의 끝에 선택적 GatewayHostName 매개 변수를 추가해야 합니다. Edge Hub 서버 인증서는 생성될 때 config.yaml의 소문자 버전 호스트 이름을 기반으로 하므로, 이름을 일치시키고 TLS 인증서 확인에 성공하려면 GatewayHostName 매개 변수를 소문자로 입력해야 합니다.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IoT Edge 인증서 계층 구조의 예

다음 스크린샷은 이 인증서 경로의 예를 보여주기 위해 투명 게이트웨이로 설정된 작동 중인 IoT Edge 장치에서 가져온 것입니다. OpenSSL은 Edge Hub에 연결하고, 유효성을 검사하며, 인증서를 덤프하는 데 사용됩니다.

![각 수준의 인증서 계층 구조 스크린샷](./media/iot-edge-certs/iotedge-cert-chain.png)

스크린샷에 표시된 인증서 깊이의 계층 구조를 확인할 수 있습니다.

| 루트 CA 인증서         | Azure IoT Hub CA 인증서 테스트 전용                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| 중간 CA 인증서 | Azure IoT Hub 중간 인증서 테스트 전용                                                                 |
| 장치 CA 인증서       | iotgateway.ca(“iotgateway”가 < gateway host name >으로 편리한 스크립트에 전달되었음)      |
| 워크로드 CA 인증서     | iotedge 워크로드 ca                                                                                       |
| Edge Hub 서버 인증서 | iotedgegw.local(config.yaml의 ‘hostname’에 일치)                                                |

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge 모듈 이해](iot-edge-modules.md)

[IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)