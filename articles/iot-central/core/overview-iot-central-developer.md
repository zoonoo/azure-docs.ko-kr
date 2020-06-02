---
title: Azure IoT Central용 디바이스 개발 | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central 애플리케이션에 연결할 디바이스를 개발하는 방법에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d29420d14a716e5a45e42dd681faa01f518f713a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665795"
---
# <a name="iot-central-device-development-overview"></a>IoT Central 디바이스 개발 개요

*이 문서는 디바이스 개발자에게 적용됩니다.*

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 개요는 IoT Central에 연결하는 디바이스에서 실행되는 코드를 구현하는 디바이스 개발자를 위해 작성되었습니다.

디바이스는 다음과 같은 기본 원칙을 사용하여 IoT Central 애플리케이션과 상호 작용합니다.

- _원격 분석 데이터_는 디바이스가 IoT Central로 전송하는 데이터입니다. 온보드 센서의 온도 값 스트림을 예로 들 수 있습니다.
- _속성_은 디바이스가 IoT Central에 보고하는 상태 값입니다. 디바이스의 현재 펌웨어 버전을 예로 들 수 있습니다. IoT Central이 디바이스에서 업데이트할 수 있는 쓰기 가능 속성도 사용할 수 있습니다.
- _명령_은 디바이스의 동작을 제어하기 위해 IoT Central에서 호출됩니다. 예를 들어 IoT Central 애플리케이션에서 디바이스 재부팅 명령을 호출할 수 있습니다.

솔루션 빌더는 원격 분석 데이터를 시각화하고, 속성을 관리하고, 명령을 호출할 수 있도록 IoT Central 웹 UI에서 대시보드와 보기를 구성하는 역할을 담당합니다.

## <a name="types-of-device"></a>디바이스 유형

다음 섹션에서는 IoT Central 애플리케이션에 연결할 수 있는 주요 디바이스 유형을 설명합니다.

### <a name="standalone-device"></a>독립 실행형 디바이스

독립 실행형 디바이스는 IoT Central에 직접 연결합니다. 독립 실행형 디바이스는 일반적으로 온보드 또는 연결된 센서의 원격 분석 데이터를 IoT Central 애플리케이션으로 보냅니다. 또한 독립 실행형 디바이스는 속성 값을 보고하고, 쓰기 가능한 속성 값을 수신하고, 명령에 응답할 수 있습니다.

### <a name="gateway-device"></a>게이트웨이 디바이스

게이트웨이 디바이스는 IoT Central 애플리케이션에 연결하는 하나 이상의 다운스트림 디바이스를 관리합니다. IoT Central을 사용하여 다운스트림 디바이스와 게이트웨이 디바이스 간의 관계를 구성합니다. 자세한 내용은 [Azure IoT Central 애플리케이션에서 새 IoT 게이트웨이 디바이스 유형 정의](./tutorial-define-gateway-device-type.md)를 참조하세요.

### <a name="edge-device"></a>에지 디바이스

에지 디바이스는 IoT Central에 직접 연결하지만, _리프 디바이스_라고 하는 다른 디바이스의 중개자 역할을 합니다. 에지 디바이스는 일반적으로 중개자 역할을 하는 리프 디바이스와 가까이에 있습니다. 다음은 에지 디바이스를 사용하는 시나리오입니다.

- IoT Central에 직접 연결할 수 없는 디바이스는 에지 디바이스를 통해 연결하도록 설정합니다. 예를 들어 리프 디바이스는 bluetooth를 사용하여 에지 디바이스에 연결한 다음, 인터넷을 통해 IoT Central에 연결할 수 있습니다.
- IoT Central로 전송되기 전에 원격 분석 데이터를 집계합니다. 이 방법은 IoT Central로 데이터를 보내는 비용을 줄이는 데 도움이 될 수 있습니다.
- 인터넷을 통해 IoT Central에 연결할 때 발생하는 대기 시간을 없애기 위해 리프 디바이스를 로컬로 제어합니다.

뿐만 아니라 에지 디바이스는 자체 원격 분석 데이터를 전송하고, 속성을 보고하고, 쓰기 가능한 속성 업데이트 및 명령에 응답할 수 있습니다.

IoT Central은 에지 디바이스만 볼 수 있고, 에지 디바이스에 연결된 리프 디바이스는 볼 수 없습니다.

자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가](./tutorial-add-edge-as-leaf-device.md)를 참조하세요.

## <a name="connect-a-device"></a>디바이스 연결

Azure IoT Central은 모든 디바이스 등록 및 연결에 [Azure IoT Hub DPS(Device Provisioning Service)](../../iot-dps/about-iot-dps.md)를 사용합니다.

DPS를 사용하면 다음과 같은 장점이 있습니다.

- IoT Central이 디바이스를 대규모로 온보딩하고 연결하는 것을 지원합니다.
- IoT Central UI를 통해 디바이스를 등록할 필요 없이 오프라인으로 디바이스 자격 증명을 생성하고 디바이스를 구성할 수 있습니다.
- 사용자의 고유한 디바이스 ID를 사용하여 IoT Central에서 디바이스를 등록할 수 있습니다. 사용자의 고유한 디바이스 ID를 사용하면 기존 백 오피스 시스템과 간편하게 통합할 수 있습니다.
- 디바이스를 IoT Central에 연결하는 한 가지 일관적인 방법이 있습니다.

자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md)을 참조하세요.

### <a name="security"></a>보안

디바이스와 IoT Central 애플리케이션 간의 연결은 [공유 액세스 서명](./concepts-get-connected.md#connect-devices-at-scale-using-sas) 또는 업계 표준 [X.509 인증서](./concepts-get-connected.md#connect-devices-using-x509-certificates)를 사용하여 보호됩니다.

### <a name="communication-protocols"></a>통신 프로토콜

디바이스에서 IoT Central에 연결하는 데 사용할 수 있는 통신 프로토콜은 MQTT, AMQP 및 HTTPS입니다. 내부적으로 IoT Central은 디바이스를 연결하기 위해 IoT 허브를 사용합니다. IoT Hub가 디바이스 연결에 대해 지원하는 통신 프로토콜에 대한 자세한 내용은 [통신 프로토콜 선택](../../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

## <a name="implement-the-device"></a>디바이스 구현

[Azure IoT 디바이스 SDK](#languages-and-sdks) 중 하나를 사용하여 디바이스의 동작을 구현할 수 있습니다. 코드는 다음을 수행해야 합니다.

- IoT Central 애플리케이션에서 디바이스를 DPS에 등록하고 DPS의 정보를 사용하여 내부 IoT 허브에 연결합니다.
- IoT Central의 디바이스 템플릿에서 지정하는 형식으로 원격 분석 데이터를 보냅니다. IoT Central은 디바이스 템플릿을 사용하여 시각화 및 분석에 원격 분석 데이터를 어떻게 사용할 것인지 결정합니다.
- 디바이스와 IoT Central 간에 속성 값을 동기화합니다. IoT Central에서 정보를 표시할 수 있도록 디바이스 템플릿은 속성 이름과 데이터 형식을 지정합니다.
- 디바이스 템플릿에서 지정하는 명령을 처리할 명령 처리기를 구현합니다. 디바이스 템플릿은 디바이스에서 사용할 명령 이름과 매개 변수를 지정합니다.

자세한 내용은 [Node.js 클라이언트 애플리케이션을 만들어 연결](./tutorial-connect-device-nodejs.md) 또는 [Python 클라이언트 애플리케이션을 만들어 연결](./tutorial-connect-device-python.md)을 참조하세요.

### <a name="languages-and-sdks"></a>언어 및 SDK

지원되는 언어와 SDK에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK의 이해 및 사용](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)을 참조하세요.

## <a name="next-steps"></a>다음 단계

디바이스 개발자이고 몇 가지 코드에 대해 자세히 알아보려면 다음 단계를 [클라이언트 애플리케이션을 만들고 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device-nodejs.md)하는 것이 좋습니다.

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
