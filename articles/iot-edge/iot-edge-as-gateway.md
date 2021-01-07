---
title: 다운스트림 디바이스의 게이트웨이 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge를 사용하여 데이터를 여러 다운스트림 디바이스에서 클라우드로 보내거나 로컬로 처리하는 투명, 불투명 또는 프록시 게이트웨이 디바이스를 만듭니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536441"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge 디바이스를 게이트웨이로 사용하는 방법

IoT Edge 장치는 게이트웨이로 작동할 수 있으며 네트워크의 다른 장치와 IoT Hub 간에 연결을 제공 합니다.

IoT Edge 허브 모듈은 IoT Hub 처럼 작동 하므로 다른 IoT Edge 장치를 비롯 하 여 IoT Hub id가 있는 모든 장치의 연결을 처리할 수 있습니다. 이러한 유형의 게이트웨이 패턴은 메시지가 다운스트림 장치 간에 게이트웨이가 없는 경우와 같이 다운스트림 장치에서 IoT Hub 전달 될 수 있으므로 *투명* 이라고 합니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 버전 1.2부터 투명 게이트웨이는 다른 IoT Edge 장치에서 다운스트림 연결을 처리할 수 있습니다.
::: moniker-end

자체 IoT Hub에 연결할 수 없거나 연결할 수 없는 장치의 경우 IoT Edge 게이트웨이에서 해당 연결을 제공할 수 있습니다. IoT Edge 장치가 들어오는 다운스트림 장치 메시지에 대 한 처리를 수행 해야 IoT Hub에 전달할 수 있으므로이 유형의 게이트웨이 패턴을 *변환* 이라고 합니다. 이러한 시나리오에는 처리 단계를 처리 하기 위해 IoT Edge 게이트웨이의 추가 모듈이 필요 합니다.

투명 및 변환 게이트웨이 패턴은 함께 사용할 수 없습니다. 단일 IoT Edge 장치는 투명 게이트웨이 및 번역 게이트웨이로 작동할 수 있습니다.

모든 게이트웨이 패턴은 다음과 같은 이점을 제공합니다.

* **Edge에서의 분석** – AI 서비스를 로컬로 사용 하 여 완전 한 성능 원격 분석을 클라우드로 보내지 않고 다운스트림 장치에서 들어오는 데이터를 처리 합니다. 고급 정보를 로컬로 검색하여 대응하고 IoT Hub에는 데이터 하위 집합만 보냅니다.
* **다운스트림 디바이스 격리** – 게이트웨이 디바이스는 모든 다운스트림 디바이스를 인터넷에 노출되지 않도록 보호할 수 있습니다. 연결 되지 않은 OT (운영 기술) 네트워크와 웹에 대 한 액세스를 제공 하는 IT (정보 기술) 네트워크 사이에 앉아 있을 수 있습니다. 마찬가지로 IoT Hub에 연결 하는 기능이 없는 장치는 게이트웨이 장치에 대신 연결할 수 있습니다.
* **연결 멀티플렉싱** - IoT Edge 게이트웨이를 통해 IoT Hub에 연결하는 모든 디바이스는 동일한 기본 연결을 사용합니다.
* **트래픽 다듬기** - IoT Hub가 트래픽을 제한할 경우 IoT Edge 디바이스는 메시지를 로컬로 유지하면서 지수 백오프를 자동으로 구현합니다. 이를 통해 트래픽이 급증할 때 솔루션에 복원력을 제공합니다.
* **오프 라인 지원** -게이트웨이 장치는 IoT Hub에 배달할 수 없는 메시지 및 쌍 업데이트를 저장 합니다.

## <a name="transparent-gateways"></a>투명 게이트웨이

투명 게이트웨이 패턴에서 이론적으로 IoT Hub에 연결할 수 있는 장치는 게이트웨이 장치에 연결할 수 있습니다. 다운스트림 장치에는 자체 IoT Hub id가 있으며 MQTT 또는 AMQP 프로토콜을 사용 하 여 연결 합니다. 게이트웨이는 디바이스와 IoT Hub 간에 통신을 전달하기만 하면 됩니다. 장치 및 사용자가 IoT Hub을 통해 상호 작용 하는 사용자는 게이트웨이가 통신을 mediating 하는 것을 인식 하지 못합니다. 이러한 인식 부족은 게이트웨이가 *투명 한* 것으로 간주 됨을 의미 합니다.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge 장치는 IoT Edge 게이트웨이의 다운스트림 일 수 없습니다.

![다이어그램-투명 게이트웨이 패턴](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

1.2.0 버전부터 IoT Edge 장치는 투명 게이트웨이를 통해 연결할 수 있습니다.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>부모 및 자식 관계

IoT Edge 게이트웨이를 연결 하는 다운스트림 장치 *자식의* *부모로* 설정 하 여 IoT Hub에서 투명 게이트웨이 관계를 선언 합니다.

부모/자식 관계는 게이트웨이 구성에서 세 지점으로 설정 됩니다.

#### <a name="cloud-identities"></a>클라우드 ID

투명 게이트웨이 시나리오의 모든 장치는 IoT Hub에 인증할 수 있도록 클라우드 id가 필요 합니다. 장치 id를 만들거나 업데이트할 때 장치의 부모 또는 자식 장치를 설정할 수 있습니다. 이 구성은 부모 게이트웨이 장치에 자식 장치에 대 한 인증을 처리 하는 권한을 부여 합니다.

자식 장치에는 하나의 부모만 있을 수 있습니다. 각 부모는 최대 100 개의 자식을 포함할 수 있습니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 장치는 투명 게이트웨이 관계의 부모와 자식을 모두 포함할 수 있습니다. 서로를 보고 하는 여러 IoT Edge 장치의 계층 구조를 만들 수 있습니다. 게이트웨이 계층의 최상위 노드에는 최대 5 개의 자식이 있을 수 있습니다. 예를 들어 IoT Edge 장치에는 아래 자식으로 연결 된 5 개의 IoT Edge 장치 계층이 있을 수 있습니다. 하지만 다섯 번째 세대의 IoT Edge 장치에는 IoT Edge 자식을 포함할 수 없습니다.
::: moniker-end

#### <a name="gateway-discovery"></a>게이트웨이 검색

자식 장치는 로컬 네트워크에서 부모 장치를 찾을 수 있어야 합니다. FQDN (정규화 된 도메인 이름) 또는 IP 주소를 사용 하는 **호스트 이름** (FQDN) 또는 IP 주소를 사용 하 여 게이트웨이 장치를 구성 합니다.

다운스트림 IoT 장치에서 연결 문자열의 **Ghostname** 매개 변수를 사용 하 여 부모 장치를 가리킵니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
다운스트림 IoT Edge 장치에서 config.xml 파일의 **parent_hostname** 매개 변수를 사용 하 여 부모 장치를 가리킵니다.
::: moniker-end

#### <a name="secure-connection"></a>보안 연결

또한 부모 및 자식 장치는 서로 연결을 인증 해야 합니다. 각 장치에는 자식 장치가 적절 한 게이트웨이에 연결 하는지 확인 하는 데 사용 하는 공유 루트 CA 인증서의 복사본이 필요 합니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
여러 IoT Edge 게이트웨이가 게이트웨이 계층 구조에서 서로 연결 되는 경우 계층의 모든 장치는 단일 인증서 체인을 사용 해야 합니다.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>투명 게이트웨이 뒤에 있는 장치 기능

IoT Edge의 메시징 파이프라인에서 작동 하는 모든 IoT Hub 기본 형식은 투명 게이트웨이 시나리오도 지원 합니다. 각 IoT Edge 게이트웨이에는 들어오는 메시지에 대 한 저장 및 전달 기능이 있습니다.

다음 표를 사용 하 여 게이트웨이 뒤에 있는 장치에 비해 다양 한 IoT Hub 기능이 장치에 대해 지원 되는지 확인 합니다.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| 기능 | IoT 디바이스 | 게이트웨이 뒤의 IoT |
| ---------- | ---------- | -------------------- |
| [장치-클라우드 (D2C) 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![예-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [클라우드-장치 (C2D) 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![예-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![예-IoT 자식 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) | ![예-IoT direct 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT direct 메서드](./media/iot-edge-as-gateway/check-yes.png) |
| [장치](../iot-hub/iot-hub-devguide-device-twins.md) 쌍 및 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 쌍 | ![예-IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) |
| [파일 업로드](../iot-hub/iot-hub-devguide-file-upload.md) | ![예-IoT 파일 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![IoT 자식 파일 업로드 안 함](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 기능 | IoT 디바이스 | 게이트웨이 뒤의 IoT | IoT Edge 디바이스 | 게이트웨이 뒤에 IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [장치-클라우드 (D2C) 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![예-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [클라우드-장치 (C2D) 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![예-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![예-IoT 자식 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![아니요-IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![아니요-IoT Edge 자식 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) | ![예-IoT direct 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT direct 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예-직접 메서드 IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT Edge direct 메서드](./media/iot-edge-as-gateway/check-yes.png) |
| [장치](../iot-hub/iot-hub-devguide-device-twins.md) 쌍 및 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 쌍 | ![예-IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예-IoT Edge 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT Edge 쌍](./media/iot-edge-as-gateway/check-yes.png) |
| [파일 업로드](../iot-hub/iot-hub-devguide-file-upload.md) | ![예-IoT 파일 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![IoT 자식 파일 업로드 안 함](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge 파일 업로드](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge 자식 파일 업로드](./media/iot-edge-as-gateway/crossout-no.png) |
| 컨테이너 이미지 가져오기 |   |   | ![예-IoT Edge 컨테이너 풀](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT Edge 컨테이너 풀](./media/iot-edge-as-gateway/check-yes.png) |
| Blob 업로드 |   |   | ![예-IoT Edge blob 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![예-자식 IoT Edge blob 업로드](./media/iot-edge-as-gateway/check-yes.png) |

**컨테이너 이미지** 를 다운로드 하 여 저장 하 고 부모 장치에서 자식 장치로 배달할 수 있습니다.

지원 번들 및 로그를 포함 한 **blob** 은 자식 장치에서 부모 장치로 업로드할 수 있습니다.

::: moniker-end

## <a name="translation-gateways"></a>변환 게이트웨이

다운스트림 장치 IoT Hub에 연결할 수 없는 경우 IoT Edge 게이트웨이가 번역기로 작동 해야 합니다. 이 패턴은 일반적으로 MQTT, AMQP 또는 HTTP를 지원 하지 않는 장치에 필요 합니다. 이러한 장치는 IoT Hub에 연결할 수 없기 때문에 일부 사전 처리 없이도 IoT Edge 허브 모듈에 연결할 수 없습니다.

일반적으로 다운스트림 장치의 하드웨어 또는 프로토콜과 관련 된 사용자 지정 또는 타사 모듈을 IoT Edge 게이트웨이에 배포 해야 합니다. 이러한 변환 모듈은 들어오는 메시지를 가져와서 IoT Hub에서 이해할 수 있는 형식으로 변환 합니다.

변환 게이트웨이에는 *프로토콜 변환* 및 *id 변환과* 같은 두 가지 패턴이 있습니다.

![다이어그램-변환 게이트웨이 패턴](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>프로토콜 변환

프로토콜 변환 게이트웨이 패턴에서 IoT Edge 게이트웨이에는 IoT Hub id가 있습니다. 변환 모듈은 다운스트림 장치에서 메시지를 수신 하 여 지원 되는 프로토콜로 변환한 다음 다운스트림 장치 대신 IoT Edge 장치에서 메시지를 보냅니다. 모든 정보는 하나의 디바이스인 게이트웨이에서 오는 것처럼 보입니다. 클라우드 애플리케이션이 디바이스별로 데이터를 분석하려는 경우 다운스트림 디바이스는 메시지에 추가 식별 정보를 포함해야 합니다. 또한 쌍 및 direct 메서드와 같은 IoT Hub 기본 형식은 다운스트림 장치가 아닌 게이트웨이 장치에 대해서만 지원 됩니다. 이 패턴의 게이트웨이는 다운스트림 장치의 id를 모호 하 게 하므로 투명 게이트웨이와 달리 *불투명* 한 것으로 간주 됩니다.

프로토콜 변환은 리소스가 제한 된 장치를 지원 합니다. 많은 기존 디바이스가 비즈니스 통찰력을 얻을 수 있는 데이터를 생산하지만, 클라우드 연결을 염두에 두고 개발되지는 않았습니다. 불투명 게이트웨이를 통해 IoT 솔루션에서이 데이터를 잠금 해제 하 고 사용할 수 있습니다.

### <a name="identity-translation"></a>ID 변환

Id 변환 게이트웨이 패턴은 프로토콜 변환을 기반으로 하지만, IoT Edge 게이트웨이는 다운스트림 장치를 대신 하 여 IoT Hub 장치 id를 제공 합니다. 변환 모듈은 다운스트림 장치에서 사용 하는 프로토콜을 이해 하 고, id를 제공 하 고, 메시지를 IoT Hub 기본 형식으로 변환 하는 일을 담당 합니다. 다운스트림 디바이스는 IoT Hub에서 쌍 및 메서드를 포함한 고급 디바이스로 표시됩니다. 사용자는 IoT Hub의 디바이스와 상호 작용할 수 있고 중간 게이트웨이 디바이스를 인식하지 못합니다.

Id 변환은 프로토콜 변환의 이점을 제공 하 고, 클라우드에서 다운스트림 장치를 완전히 관리할 수 있도록 합니다. IoT 솔루션의 모든 디바이스는 사용하는 프로토콜에 관계없이 IoT Hub에 표시됩니다.

### <a name="device-capabilities-behind-translation-gateways"></a>변환 게이트웨이 뒤의 장치 기능

다음 표에서는 두 변환 게이트웨이 패턴의 다운스트림 장치에 IoT Hub 기능이 확장 되는 방법에 대해 설명 합니다.

| 기능 | 프로토콜 변환 | ID 변환 |
| ---------- | -------------------- | -------------------- |
| IoT Hub ID 레지스트리에 저장된 ID | 게이트웨이 디바이스의 ID만 | 연결된 모든 디바이스의 ID |
| 디바이스 쌍 | 게이트웨이에만 디바이스 및 모듈 쌍이 있음 | 연결된 각 디바이스에 자체 디바이스 쌍이 있음 |
| 직접 방식 및 클라우드-디바이스 메시지 | 클라우드가 게이트웨이 디바이스만 처리할 수 있음 | 클라우드가 연결된 각 디바이스를 개별적으로 처리할 수 있음 |
| [IoT Hub 제한 및 할당량](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 게이트웨이 디바이스에 적용 | 각 디바이스에 적용 |

프로토콜 변환 패턴을 사용 하는 경우 해당 게이트웨이를 통해 연결 되는 모든 장치는 동일한 클라우드-장치 큐를 공유 합니다 .이 큐에는 최대 50 개의 메시지가 포함 될 수 있습니다. 각 필드 게이트웨이를 통해 연결 하는 장치가 거의 없고 클라우드-장치 트래픽이 적은 경우에만이 패턴을 사용 합니다.

IoT Edge 런타임은 프로토콜 또는 id 변환 기능을 포함 하지 않습니다. 이러한 패턴에는 자주 사용 되는 하드웨어 및 프로토콜과 관련 된 사용자 지정 또는 타사 모듈이 필요 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 에는 선택할 수 있는 몇 가지 프로토콜 변환 모듈이 포함 되어 있습니다. Id 변환 패턴을 사용 하는 샘플은 [Azure IoT Edge LoRaWAN 스타터 Kit](https://github.com/Azure/iotedge-lorawan-starterkit)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

투명 게이트웨이를 설정 하는 세 가지 단계를 알아봅니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [Azure IoT Hub에 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)
* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)
