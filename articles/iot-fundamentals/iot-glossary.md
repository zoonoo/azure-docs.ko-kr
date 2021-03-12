---
title: Azure IoT 용어 | Microsoft Docs
description: 개발자 가이드-Azure IoT 문서에 사용 된 몇 가지 일반적인 용어에 대해 설명 하는 용어입니다.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 3e8a2ac93e9fea6ad045030759be894617557658
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022089"
---
# <a name="glossary-of-iot-terms"></a>IoT 용어 용어집

이 문서에서는 IoT 문서에 사용 된 몇 가지 일반적인 용어를 나열 합니다.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>고급 메시지 큐 프로토콜

[AMQP(고급 메시지 큐 프로토콜)](https://www.amqp.org/)는 [IoT Hub](#iot-hub)에서 디바이스와 통신을 위해 지원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="automatic-device-management"></a>자동 장치 관리

Azure IoT Hub에서 자동 디바이스 관리는 전체 수명 주기를 통해 대규모 디바이스를 관리하는 반복적이고 복잡한 작업을 자동화합니다. 자동 디바이스 관리를 사용하여 해당 속성을 기반으로 디바이스 집합을 대상으로 지정하고, 원하는 구성을 정의하고, 범위에 나올 때마다 IoT Hub에서 디바이스를 업데이트하도록 할 수 있습니다.  [자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md) 및 [IoT Edge 자동 배포](../iot-edge/how-to-deploy-at-scale.md)로 구성됩니다.

### <a name="automatic-device-configuration"></a>자동 디바이스 구성

솔루션 백 엔드는 [자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md)을 사용하여 [디바이스 쌍](#device-twin)의 집합에 원하는 속성을 할당하고 시스템 메트릭 및 사용자 지정 메트릭을 사용하여 상태를 보고할 수 있습니다. 

### <a name="azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK

여러 언어로 된 _디바이스 SDK_ 가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [디바이스 앱](#device-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 디바이스 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 디바이스 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

[Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer) 는 장치에서 보내는 원격 분석을 보고, 장치 속성을 사용 하 고, 명령을 호출 하는 데 사용 됩니다. 탐색기를 사용 하 여 장치를 조작 및 테스트 하 고 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)를 관리할 수도 있습니다.

### <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK

여러 언어로 된 _장치 SDK_ 가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [백 엔드 앱](#back-end-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 서비스 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 서비스 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)는 VS Code에서 디바이스 및 Azure IoT Hub 관리에 도움이 되는 플랫폼 간 오픈 소스 Visual Studio Code 확장입니다. IoT 개발자는 Azure IoT Tools를 사용하여 손쉽게 VS Code에서 IoT 프로젝트를 개발할 수 있습니다.

## <a name="b"></a>b

### <a name="back-end-app"></a>백 엔드 앱

[IoT Hub](#iot-hub)의 컨텍스트에서 백 엔드 앱은 IoT Hub에서 서비스 지향 엔드포인트 중 하나에 연결되는 앱입니다. 예를 들어 백 엔드 앱은 [디바이스-클라우드](#device-to-cloud) 메시지를 검색하거나 [ID 레지스트리](#identity-registry)를 관리할 수 있습니다. 일반적으로 백 엔드 앱은 클라우드에서 실행되지만 대부분의 자습서에 나오는 백 엔드 앱은 로컬 개발 환경에서 실행 중인 콘솔 앱입니다.

### <a name="built-in-endpoints"></a>기본 제공 엔드포인트

모든 IoT Hub에는 Event Hub와 호환되는 기본 제공 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)가 있습니다. Event Hubs와 함께 작동하는 모든 메커니즘을 사용하여 이 엔드포인트에서 디바이스-클라우드 메시지를 읽을 수 있습니다.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>클라우드 게이트웨이

클라우드 게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. 클라우드 게이트웨이는 디바이스에 대해 로컬로 시행되는 [필드 게이트웨이](#field-gateway)와 달리, 클라우드에서 호스트됩니다. 클라우드 게이트웨이를 위한 일반 사용 사례는 디바이스에 대한 프로토콜 변환을 구현하는 것입니다.

### <a name="cloud-to-device"></a>클라우드-디바이스

IoT Hub에서 연결된 디바이스로 전송되는 메시지를 참조하세요. 대개 이러한 메시지는 디바이스에 작업을 수행하도록 하는 명령입니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="commands"></a>명령

IoT 플러그 앤 플레이에서 [인터페이스](#interface) 에 정의 된 명령은 [디지털](#digital-twin)쌍에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어, 디바이스를 재부팅하는 명령이 여기에 해당합니다.

### <a name="component"></a>구성 요소

IoT 플러그 앤 플레이에서 구성 요소를 사용 하 여 다른 인터페이스의 어셈블리로 모델 [인터페이스](#interface) 를 빌드할 수 있습니다. [장치 모델](#device-model) 은 여러 인터페이스를 구성 요소로 결합할 수 있습니다. 예를 들어 모델에는 스위치 구성 요소 및 자동 온도 조절기 구성 요소가 포함 될 수 있습니다. 모델의 여러 구성 요소는 동일한 인터페이스 유형을 사용할 수도 있습니다. 예를 들어 모델에는 두 개의 자동 온도 조절기 구성 요소가 포함 될 수 있습니다.

### <a name="configuration"></a>구성

[자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md)의 컨텍스트에서 IoT Hub 내의 구성은 디바이스 쌍의 집합에 대해 원하는 구성을 정의하고 상태 및 진행률을 보고하도록 메트릭 집합을 제공합니다.

### <a name="connection-string"></a>연결 문자열

앱 코드에서 연결 문자열을 사용하여 엔드포인트에 연결하는 데 필요한 정보를 캡슐화할 수 있습니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- *디바이스 연결 문자열* 은 IoT hub의 디바이스 연결 엔드포인트에 연결하는 디바이스를 사용하도록 설정합니다.

- *IoT Hub 연결 문자열* 은 IoT hub의 서비스 연결 엔드포인트에 연결하는 백 엔드 앱을 사용하도록 설정합니다.

### <a name="custom-endpoints"></a>사용자 지정 엔드포인트

IoT Hub에 사용자 지정 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)를 만들어 [라우팅 규칙](#routing-rules)으로 발송된 메시지를 배달할 수 있습니다. 사용자 지정 엔드포인트는 Event Hub, Service Bus 큐 또는 Service Bus 토픽에 직접 연결됩니다.

### <a name="custom-gateway"></a>사용자 지정 게이트웨이

게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. Azure IoT Edge를 사용하여 메시지, 사용자 지정 프로토콜 변환 및 에지의 기타 프로세스를 처리하도록 사용자 지정 논리를 구현하는 사용자 지정 게이트웨이를 빌드할 수 있습니다.

## <a name="d"></a>D

### <a name="data-point-message"></a>데이터 요소 메시지

데이터 요소 메시지는 풍속 또는 온도와 같은 [원격 분석](#telemetry) 데이터를 포함하는 [클라우드-디바이스](#device-to-cloud) 메시지입니다.

### <a name="default-component"></a>기본 구성 요소

IoT 플러그 앤 플레이에서 모든 [장치 모델](#device-model) 에는 기본 구성 요소가 있습니다. 단순 장치 모델에는 기본 구성 요소만 있습니다. 이러한 모델은 구성 요소 장치가 아닌 것으로 알려져 있습니다. 더 복잡 한 모델에는 기본 구성 요소 아래에 중첩 된 여러 구성 요소가 있습니다.

### <a name="device-certification"></a>디바이스 인증

IoT 플러그 앤 플레이 디바이스 인증 프로그램은 디바이스가 IoT 플러그 앤 플레이 인증 요구 사항을 충족하는지 확인합니다. 인증된 디바이스를 공용 [Azure IoT 디바이스 카탈로그 인증](https://aka.ms/devicecatalog)에 추가할 수 있습니다.

### <a name="device-model"></a>디바이스 모델

장치 모델은 [디지털 Twins 정의 언어](#digital-twins-definition-language) 를 사용 하 여 IoT 플러그 앤 플레이 장치의 기능을 설명 합니다. 간단한 장치 모델은 단일 인터페이스를 사용 하 여 장치 기능을 설명 합니다. 더 복잡 한 장치 모델에는 여러 구성 요소가 포함 되며, 각 구성 요소는 기능 집합을 설명 합니다. 자세히 알아보려면 [모델의 IoT 플러그 앤 플레이 구성 요소](../iot-pnp/concepts-components.md)를 참조 하세요.

### <a name="device-builder"></a>장치 빌더

장치 빌더는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에서 실행 되도록 코드를 구현할 때 [장치 모델](#device-model) 및 [인터페이스](#interface) 를 사용 합니다. 일반적으로 장치 빌더는 [Azure IoT 장치 sdk](#azure-iot-device-sdks) 중 하나를 사용 하 여 장치 클라이언트를 구현 합니다.

### <a name="device-modeling"></a>디바이스 모델링

[장치 빌더](#device-builder) 또는 [모듈 빌더](#module-builder)는 [디지털 twins 정의 언어](#digital-twins-definition-language) 를 사용 하 여 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)기능을 모델링 합니다. [솔루션 빌더](#solution-builder) 는 모델에서 IoT 솔루션을 구성할 수 있습니다.

### <a name="desired-configuration"></a>desired 구성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 구성은 디바이스와 동기화해야 하는 디바이스의 속성 및 메타데이터 전체 집합을 나타냅니다.

### <a name="desired-properties"></a>Desired 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 속성은 [reported 속성](#reported-properties)과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용되는 디바이스 쌍의 하위 섹션입니다. desired 속성은 [백 엔드 앱](#back-end-app)에서만 설정할 수 있고 [디바이스 앱](#device-app)에서 관찰할 수 있습니다.

### <a name="device-to-cloud"></a>디바이스-클라우드

연결된 디바이스에서 [IoT Hub](#iot-hub)로 전송되는 메시지를 참조하세요. 이러한 메시지는 [데이터 요소](#data-point-message) 또는 [대화형](#interactive-message) 메시지일 수 있습니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="device"></a>디바이스

IoT의 컨텍스트에서 일반적으로 디바이스는 데이터를 수집하거나 다른 디바이스를 제어할 수 있는 소규모의 독립 실행형 컴퓨팅 디바이스입니다. 예를 들어 디바이스는 환경 모니터링 디바이스이거나 온실에서 급수 및 통풍 시스템을 위한 컨트롤러일 수 있습니다. [디바이스 카탈로그](https://catalog.azureiotsolutions.com/)는 [IoT Hub](#iot-hub)에서 작동이 인증된 하드웨어 디바이스 목록을 제공합니다.

### <a name="device-app"></a>디바이스 앱

디바이스 앱은 [디바이스](#device)에서 실행되며 [IoT Hub](#iot-hub)와의 통신을 처리합니다. 일반적으로 디바이스 앱을 구현할 때 [Azure IoT 디바이스 SDK](#azure-iot-device-sdks) 중 하나를 사용합니다. 대부분의 IoT 자습서에서는 편의를 위해 [시뮬레이트된 디바이스](#simulated-device)를 사용합니다.

### <a name="device-condition"></a>디바이스 조건

[장치 앱](#device-app)에서 보고 하는 현재 사용 중인 연결 방법과 같은 장치 상태 정보를 나타냅니다. [디바이스 앱](#device-app)은 해당 기능도 보고할 수 있습니다. 디바이스 쌍을 사용하여 조건 및 기능 정보를 쿼리할 수 있습니다.

### <a name="device-data"></a>디바이스 데이터

디바이스 데이터는 IoT Hub [ID 레지스트리](#identity-registry)에 저장된 디바이스 단위 데이터를 나타냅니다. 이 데이터를 가져오고 내보낼 수 있습니다.

### <a name="device-identity"></a>디바이스 ID

디바이스 ID는 [ID 레지스트리](#identity-registry)에 등록된 모든 디바이스에 할당된 고유한 식별자입니다.

### <a name="device-management"></a>디바이스 관리

디바이스 관리에는 계획, 프로비전, 구성, 모니터링 및 사용 중지를 비롯하여 IoT 솔루션에서 디바이스 관리와 관련된 전체 수명 주기가 포함됩니다.

### <a name="device-management-patterns"></a>디바이스 관리 패턴

[IoT Hub](#iot-hub)를 통해 디바이스에서 재부팅, 공장 재설정 수행 및 펌웨어 업데이트 수정을 비롯한 일반적인 디바이스 관리 패턴을 수행할 수 있습니다.

### <a name="device-rest-api"></a>디바이스 REST API

디바이스에서 [디바이스 REST API](/rest/api/iothub/device)를 사용하여 디바이스-클라우드 메시지를 IoT Hub로 보내고 IoT Hub에서 [클라우드-디바이스](#cloud-to-device) 메시지를 수신할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [디바이스 SDK](#azure-iot-device-sdks)를 사용해야 합니다.

### <a name="device-provisioning"></a>디바이스 프로비전

장치 프로 비전은 솔루션의 저장소에 초기 [장치 데이터](#device-data) 를 추가 하는 프로세스입니다. 새 장치를 허브에 연결할 수 있게 하려면 장치 ID 및 키를 IoT Hub [id 레지스트리에](#identity-registry)추가 해야 합니다. 프로비전 프로세스의 일부로, 다른 솔루션 저장소에서 디바이스 특정 데이터를 초기화해야 할 수 있습니다.

### <a name="device-twin"></a>디바이스 쌍

디바이스 쌍은 디바이스의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 IoT Hub에 프로비전하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다. 디바이스 쌍을 통해 디바이스 및 솔루션 백 엔드 간의 디바이스 조건 및 구성을 동기화할 수 있습니다. 장치 쌍을 쿼리하여 특정 장치를 찾고 장기 실행 작업의 상태를 확인할 수 있습니다.

### <a name="direct-method"></a>직접 메서드

[직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)는 IoT Hub에서 API를 호출하여 디바이스에서 실행할 메서드를 트리거하는 방법입니다.

### <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 물리적 개체를 나타내는 디지털 데이터의 컬렉션입니다. 물리적 개체의 변경 내용은 디지털 쌍에 반영 됩니다. 일부 시나리오에서는 디지털 쌍을 사용 하 여 물리적 개체를 조작할 수 있습니다. [Azure Digital twins 서비스](../digital-twins/index.yml) 는 디지털 Twins [정의 언어로](#digital-twins-definition-language) 표현 된 모델을 사용 하 여 디지털 쌍을 사용 하는 다양 한 클라우드 기반 솔루션을 사용 하도록 설정 합니다. [IoT 플러그 앤 플레이](../iot-pnp/index.yml) 장치에는 dtdl [장치 모델](#device-model)에 설명 된 디지털 쌍이 있습니다.

### <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[Iot 플러그 앤 플레이 장치가](#iot-plug-and-play-device) iot hub에 연결 된 경우 허브는 해당 라우팅 기능을 사용 하 여 디지털 쌍 변경 내용에 대 한 알림을 보낼 수 있습니다. 예를 들어, 장치에서 [속성](#properties) 값이 변경 될 때마다 이벤트 허브와 같은 끝점에 알림을 보낼 수 IoT Hub.

### <a name="digital-twins-definition-language"></a>디지털 Twins 정의 언어

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에 대한 모델 및 인터페이스를 설명하는 언어입니다. 디지털 쌍 [의](#digital-twin) 기능을 설명 하 고 IoT 플랫폼과 iot 솔루션을 사용 하도록 설정 하려면 [디지털 쌍 정의 언어 버전 2](https://github.com/Azure/opendigitaltwins-dtdl) 를 사용 하 여 엔터티의 의미 체계를 사용 합니다.

### <a name="digital-twin-route"></a>디지털 쌍 경로

IoT hub에서 설정 되어 [디지털 쌍 변경 이벤트](#digital-twin-change-events) 와 끝점 (예: 이벤트 허브)을 전달 합니다.

## <a name="e"></a>E

### <a name="endpoint"></a>엔드포인트

IoT Hub는 앱을 IoT Hub에 연결할 수 있도록 해주는 여러 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)를 노출합니다. 디바이스가 [디바이스-클라우드](#device-to-cloud) 메시지 보내기 및 [클라우드-디바이스](#cloud-to-device) 메시지 받기와 같은 작업을 수행할 수 있도록 해주는 디바이스 지향 엔드포인트가 있습니다. [백 엔드 앱](#back-end-app)이 [디바이스 ID](#device-identity) 관리 및 디바이스 쌍 관리와 같은 작업을 수행할 수 있도록 하는 서비스 지향 관리 엔드포인트가 있습니다. 디바이스-클라우드 메시지 를 읽기 위한 서비스 지향 [기본 제공 엔드포인트](#built-in-endpoints)가 있습니다. [사용자 지정 엔드포인트](#custom-endpoints)를 만들어 [라우팅 규칙](#routing-rules)으로 발송된 디바이스-클라우드 메시지를 받을 수 있습니다.

### <a name="event-hub-compatible-endpoint"></a>Event Hub 호환 엔드포인트

IoT hub로 전송 되는 [장치-클라우드](#device-to-cloud) 메시지를 읽으려면 허브에 있는 끝점에 연결 하 고 Event hub 호환 메서드를 사용 하 여 해당 메시지를 읽을 수 있습니다. 이벤트 허브 호환 방법에는 [Event Hubs sdk](../event-hubs/event-hubs-programming-guide.md) 및 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)사용이 포함 됩니다.

## <a name="f"></a>F

### <a name="field-gateway"></a>필드 게이트웨이

필드 게이트웨이는 [IoT Hub](#iot-hub) 에 직접 연결할 수 없는 장치에 대 한 연결을 가능 하 게 하며 일반적으로 장치와 함께 로컬로 배포 됩니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요.

## <a name="g"></a>G

### <a name="gateway"></a>게이트웨이

게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. 참고 항목: [필드 게이트웨이](#field-gateway), [클라우드 게이트웨이](#cloud-gateway) 및 [사용자 지정 게이트웨이](#custom-gateway)

## <a name="i"></a>I

### <a name="identity-registry"></a>ID 레지스트리

[ID 레지스트리](../iot-hub/iot-hub-devguide-identity-registry.md)는 IoT Hub에 연결이 허용된 개별 디바이스에 대한 정보를 저장하는 IoT Hub의 기본 제공 구성 요소입니다.

### <a name="interactive-message"></a>대화형 메시지

대화형 메시지는 솔루션 백 엔드에서 즉각적인 작업을 트리거하는 [클라우드-디바이스](#cloud-to-device) 메시지입니다. 예를 들어 디바이스는 CRM 시스템에 자동으로 로깅되는 실패에 대한 경보를 보낼 수 있습니다.

### <a name="interface"></a>인터페이스

IoT 플러그 앤 플레이에서 인터페이스는 [iot 플러그 앤 플레이 장치](#iot-plug-and-play-device) 또는 [디지털](#digital-twin)쌍에 의해 구현 되는 관련 기능을 설명 합니다. 여러 [장치 모델](#device-model)에서 인터페이스를 재사용할 수 있습니다. 인터페이스는 장치 모델에서 사용 되는 경우 장치의 [구성 요소](#component) 를 정의 합니다. 단순 장치에는 기본 인터페이스만 포함 됩니다.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge에서는 클라우드 방식을 통해 Azure 서비스 및 솔루션별 코드를 온-프레미스 디바이스에 배포할 수 있습니다. [IoT Edge 장치](#iot-edge-device) 는 데이터를 클라우드로 보내기 전에 다른 장치의 데이터를 집계 하 여 컴퓨팅 및 분석을 수행할 수 있습니다. 자세한 내용은 [Azure IoT Edge](../iot-edge/index.yml)를 참조 하세요.

### <a name="iot-edge-agent"></a>IoT Edge 에이전트

모듈 배포 및 모니터링을 담당하는 IoT Edge 런타임의 부분입니다.

### <a name="iot-edge-device"></a>IoT Edge 디바이스

IoT Edge 장치는 컨테이너 화 된 [IoT Edge 모듈](#iot-edge-module) 을 사용 하 여 Azure 서비스, 타사 서비스 또는 자신의 코드를 실행 합니다. IoT Edge 장치에서 [IoT Edge 런타임은](#iot-edge-runtime) 모듈을 관리 합니다. 클라우드에서 IoT Edge 장치를 원격으로 모니터링 하 고 관리할 수 있습니다.

### <a name="iot-edge-automatic-deployment"></a>IoT Edge 자동 배포

IoT Edge 자동 배포는 IoT Edge 디바이스의 대상 집합이 IoT Edge 모듈 집합을 실행하도록 구성합니다. 각 배포는 새 디바이스가 만들어지거나 대상 조건과 일치하도록 수정되더라도 대상 조건과 일치하는 모든 디바이스가 지정된 모듈 집합을 실행 중인지 계속 확인합니다. 각 IoT Edge 디바이스는 해당 대상 조건을 충족하는 가장 높은 우선 순위 배포만 수신합니다. [IoT Edge 자동 배포](../iot-edge/module-deployment-monitoring.md)에 대해 자세히 알아보세요.

### <a name="iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트

모듈, 경로 및 관련된 모듈의 원하는 속성 집합을 배포할 하나 이상의 IoT Edge 디바이스 쌍에 복사될 정보를 포함하는 Json 문서입니다.

### <a name="iot-edge-gateway-device"></a>IoT Edge 게이트웨이 디바이스

다운스트림 디바이스가 있는 IoT Edge 디바이스입니다. 다운스트림 디바이스는 IoT Edge 디바이스일 수도 있고 그렇지 않을 수도 있습니다.

### <a name="iot-edge-hub"></a>IoT Edge 허브

모듈 간 통신, 업스트림(IoT Hub 쪽) 및 다운스트림(IoT Hub 반대쪽) 통신을 담당하는 IoT Edge 런타임의 일부입니다.

### <a name="iot-edge-leaf-device"></a>IoT Edge 리프 디바이스

다운스트림 디바이스가 없는 IoT Edge 디바이스입니다.

### <a name="iot-edge-module"></a>IoT Edge 모듈

IoT Edge 모듈은 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다. 디바이스에서 메시지 수집, 메시지 변환 또는 IoT Hub로 메시지 보내기와 같은 특정 작업을 수행합니다. 다른 모듈와 통신하고 IoT Edge 런타임에 데이터를 보냅니다. [IoT Edge 모듈을 개발하기 위한 요구 사항 및 도구 이해](../iot-edge/module-development.md)

### <a name="iot-edge-module-identity"></a>IoT Edge 모듈 ID

모듈에서 Edge Hub 또는 IoT Hub로부터 인증을 받는 데 사용되는 보안 자격 증명 및 존재 여부를 자세히 설명하는 IoT Hub 모듈 ID 레지스트리의 레코드입니다.

### <a name="iot-edge-module-image"></a>IoT Edge 모듈 이미지

IoT Edge 런타임에서 모듈 인스턴스를 인스턴스화하는 데 사용하는 docker 이미지입니다.

### <a name="iot-edge-module-twin"></a>IoT Edge 모듈 쌍

IoT Hub에 유지되며 Json 문서 모듈 인스턴스에 대한 상태 정보를 저장하는 Json 문서입니다.

### <a name="iot-edge-priority"></a>IoT Edge 우선 순위

두 개의 IoT Edge 배포가 같은 디바이스를 대상으로 하는 경우 우선 순위가 더 높은 배포가 적용됩니다. 두 배포의 우선 순위가 동일한 경우 만든 날짜가 나중인 배포가 적용됩니다. [우선 순위](../iot-edge/module-deployment-monitoring.md#priority)에 대해 자세히 알아보세요.

### <a name="iot-edge-runtime"></a>IoT Edge 런타임

IoT Edge 런타임은 Microsoft가 IoT Edge 디바이스에 설치되도록 배포하는 모든 항목입니다. 여기에는 Edge 에이전트, Edge 허브 및 IoT Edge 보안 디먼이 포함됩니다.

### <a name="iot-edge-set-modules-to-a-single-device"></a>단일 디바이스에 대한 IoT Edge 집합 모듈

하나의 장치 모듈 쌍에 IoT Edge 매니페스트의 콘텐츠를 복사 하는 작업입니다. 기본 API는 단순히 IoT Edge 매니페스트를 입력으로 사용 하는 일반 적용 구성입니다.

### <a name="iot-edge-target-condition"></a>IoT Edge 대상 조건

IoT Edge 배포에서 대상 조건은 배포의 대상 장치를 선택 하기 위한 장치 쌍 태그의 부울 조건입니다 (예: **environment = prod**). 대상 조건은 요구 사항을 충족 하는 새 장치를 포함 하거나 더 이상 수행 하지 않는 장치를 제거 하기 위해 지속적으로 평가 됩니다. [대상 조건](../iot-edge/module-deployment-monitoring.md#target-condition)에 대해 자세히 알아보세요.

### <a name="iot-hub"></a>IoT Hub

IoT Hub는 수백만의 디바이스와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 Azure 서비스입니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요. Azure 구독을 사용하여 IoT 메시징 워크로드를 처리하는 IoT Hub를 만들 수 있습니다.

### <a name="iot-hub-metrics"></a>IoT Hub 메트릭

IoT Hub 메트릭은 Azure 구독의 IoT Hub 상태에 대한 데이터를 제공합니다. IoT Hub 메트릭을 통해 서비스와 연결된 디바이스의 전반적인 상태를 평가할 수 있습니다. IoT Hub 메트릭을 통해 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 조사할 수 있습니다. 자세히 알아보려면 [IoT Hub 모니터링](../iot-hub/monitor-iot-hub.md)을 참조하세요.

### <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

[IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md) 는 [작업](#job), 디지털 쌍 및 장치 쌍을 쿼리할 수 있도록 하는 SQL 유사 언어입니다.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub 리소스 REST API

[IoT Hub 리소스 REST API](/rest/api/iothub/iothubresource)를 사용하여 허브 만들기, 업데이트 및 삭제와 같은 작업을 수행하는 Azure 구독에서 IoT Hub를 관리할 수 있습니다.

### <a name="iot-solution-accelerators"></a>IoT 솔루션 가속기

Azure IoT 솔루션 가속기는 여러 Azure 서비스를 솔루션으로 함께 패키징합니다. 이러한 솔루션을 통해 일반적인 IoT 시나리오의 엔드투엔드 구현을 빠르게 시작합니다. 자세한 내용은 [Azure IoT 솔루션 가속기란?](../iot-accelerators/about-iot-accelerators.md)을 참조하세요.

### <a name="the-iot-extension-for-azure-cli"></a>Azure CLI에 대한 IoT 확장. 

[Azure CLI에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension)은 플랫폼 간 명령줄 도구입니다. 이 도구를 통해 [ID 레지스트리](#identity-registry)에서 디바이스를 관리하고 디바이스에서 메시지 및 파일을 보내고 받으며 IoT Hub 작업을 모니터링할 수 있습니다.

### <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

IoT 플러그 앤 플레이 브리지는 Windows 또는 Linux 게이트웨이에 연결 된 기존 센서와 주변 장치를 [IoT 플러그 앤 플레이 장치로](#iot-plug-and-play-device)연결 하는 데 사용할 수 있는 오픈 소스 응용 프로그램입니다.

### <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스

IoT 플러그 앤 플레이 장치는 일반적으로 데이터를 수집 하거나 다른 장치를 제어 하 고 [장치 모델](#device-model)을 구현 하는 소프트웨어 또는 펌웨어를 실행 하는 소규모의 독립 실행형 컴퓨팅 장치입니다.  예를 들어, IoT 플러그 앤 플레이 디바이스는 환경 모니터링 디바이스이거나 스마트 농업 관개 시스템용 컨트롤러일 수 있습니다. IoT 플러그 앤 플레이 장치는 직접 또는 IoT Edge 모듈로 구현할 수 있습니다. IoT 플러그 앤 플레이 디바이스에서 명령을 하고 제어하고 데이터를 수신하기 위한 클라우드 호스티트 IoT 솔루션을 작성할 수 있습니다.

### <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 [장치](#iot-plug-and-play-device) 는 솔루션을 사용 하 여 데이터를 교환할 때 일련의 규칙을 따라야 합니다.

## <a name="j"></a>J

### <a name="job"></a>작업

솔루션 백 엔드에서는 IoT Hub에 등록된 디바이스 집합에서의 활동을 예약 및 추적하는 [작업](../iot-hub/iot-hub-devguide-jobs.md)을 사용할 수 있습니다. 활동에는 디바이스 쌍 [desired 속성](#desired-properties) 업데이트, 디바이스 쌍 [태그](#tags) 업데이트 및 [직접 메서드](#direct-method) 호출이 포함됩니다. [IoT Hub](#iot-hub)는 [ID 레지스트리](#identity-registry)에서 [가져오고 내보내는](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) 작업도 사용합니다.

## <a name="m"></a>M

### <a name="model-id"></a>모델 ID

IoT 플러그 앤 플레이 장치가 IoT Hub에 연결 하는 경우 구현 하는 [Dtdl](#digital-twins-definition-language) 모델의 **모델 ID** 를 보냅니다. 이 ID를 사용 하면 솔루션에서 장치 모델을 찾을 수 있습니다.

### <a name="model-repository"></a>모델 리포지토리

모델 리포지토리는 [장치 모델](#device-model) 및 [인터페이스](#interface)를 저장 합니다.

### <a name="model-repository-rest-api"></a>Model repository REST API

모델 리포지토리를 관리 하 고 상호 작용 하기 위한 API입니다. 예를 들어 API를 사용 하 여 [장치 모델](#device-model)을 추가 하 고 검색할 수 있습니다.

### <a name="module-builder"></a>모듈 작성기

모듈 빌더는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에서 실행 되도록 코드를 구현할 때 [장치 모델](#device-model) 및 [인터페이스](#interface) 를 사용 합니다. 모듈 빌더는 코드를 모듈 또는 IoT Edge 모듈로 구현 하 여 장치의 IoT Edge 런타임에 배포 합니다.

### <a name="modules"></a>모듈

디바이스 쪽에서 IoT Hub 디바이스 SDK를 사용하면 각각 IoT Hub에 대한 독립적 연결을 여는 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 만들 수 있습니다. 이 함수를 사용하면 디바이스의 여러 구성 요소에 별도의 네임스페이스를 사용할 수 있습니다.

모듈 ID 및 모듈 쌍은 [디바이스 ID](#device-identity) 및 [디바이스 쌍](#device-twin)과 동일한 기능을 제공하지만 세분성이 더 높습니다. 이처럼 높은 세분성을 통해 여러 구성 요소를 관리하는 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같은 가능한 디바이스에서 이러한 각 구성 요소의 구성과 조건을 격리할 수 있습니다.

### <a name="module-identity"></a>모듈 ID

모듈 id는 장치에 속한 모든 모듈에 할당 되는 고유 식별자입니다. 또한 모듈 ID는 [ID 레지스트리](#identity-registry)에도 등록됩니다.

### <a name="module-twin"></a>모듈 쌍

디바이스 쌍과 유사하게 모듈 쌍은 모듈의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 IoT 허브의 디바이스 ID에서 프로비전하는 각 모듈 ID에 대해 모듈 쌍을 유지합니다. 모듈 쌍을 통해 모듈 및 솔루션 백 엔드 간의 모듈 조건 및 구성을 동기화할 수 있습니다. 모듈 쌍을 쿼리하여 특정 모듈을 찾고 장기 실행 중인 작업의 상태를 쿼리할 수 있습니다.

### <a name="mqtt"></a>MQTT

[Mqtt](https://mqtt.org/) 는 장치와의 통신을 위해 [IoT Hub](#iot-hub) 지 원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="o"></a>O

### <a name="operations-monitoring"></a>작업 모니터링

IoT Hub [작업 모니터링](../iot-hub/iot-hub-operations-monitoring.md) 을 사용 하면 실시간으로 IoT Hub에 대 한 작업의 상태를 모니터링할 수 있습니다. [IoT Hub](#iot-hub) 는 여러 가지 작업 범주에 걸쳐 이벤트를 추적 합니다. 하나 이상의 범주에서 IoT Hub의 엔드포인트로 처리할 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

## <a name="p"></a>P

### <a name="physical-device"></a>물리적 디바이스

물리적 디바이스는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 디바이스입니다. 편의를 위해 많은 IoT Hub 자습서에서 [시뮬레이션된 디바이스](#simulated-device)를 사용하여 로컬 머신에서 샘플을 실행할 수 있도록 합니다.

### <a name="primary-and-secondary-keys"></a>기본 및 보조 키

IoT Hub에서 디바이스 지향 또는 서비스 지향 엔드포인트에 연결하는 경우 [연결 문자열](#connection-string)에는 액세스 권한을 부여하는 키가 포함됩니다. 디바이스를 [ID 레지스트리](#identity-registry)에 추가하거나 허브에 [공유 액세스 정책](#shared-access-policy)을 추가하는 경우 서비스에서 기본 및 보조 키를 생성합니다. 두 가지 키를 통해 키를 업데이트할 때 IoT Hub에 대한 액세스 권한을 잃지 않고도 한 키에서 다른 키로 롤오버가 가능합니다.

### <a name="properties"></a>속성

속성은 디지털 쌍의 일부 상태를 나타내는 [인터페이스](#interface)에 정의된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다.  경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정됩니다.

### <a name="protocol-gateway"></a>프로토콜 게이트웨이

프로토콜 게이트웨이는 일반적으로 클라우드에 배포 되며 [IoT Hub](#iot-hub)에 연결 하는 장치에 대 한 프로토콜 변환 서비스를 제공 합니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요.

## <a name="r"></a>R

### <a name="reported-configuration"></a>Reported 구성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 구성은 솔루션 백 엔드에 보고해야 하는 디바이스 쌍의 속성 및 메타데이터 집합 전체를 나타냅니다.

### <a name="reported-properties"></a>reported 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 속성은 [desired 속성](#desired-properties)과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용되는 디바이스 쌍의 하위 섹션입니다. reported 속성은 [디바이스 앱](#device-app)에서만 설정할 수 있고 [백 엔드 앱](#back-end-app)에서 읽고 쿼리할 수 있습니다.

### <a name="retry-policy"></a>재시도 정책

클라우드 서비스에 연결할 때는 재시도 정책을 사용하여 [일시적인 오류](/azure/architecture/best-practices/transient-faults)를 처리합니다.

### <a name="routing-rules"></a>라우팅 규칙

IoT Hub에서 [기본 제공 엔드포인트](#built-in-endpoints) 또는 [사용자 지정 엔드포인트](#custom-endpoints)로 디바이스-클라우드 메시지를 라우팅하여 솔루션 백 엔드에서 처리하도록 [라우팅 규칙](../iot-hub/iot-hub-devguide-messages-read-custom.md)을 구성합니다.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN은 AMQP 프로토콜에서 보안 토큰을 전송하는 데 사용하는 프로토콜입니다.

### <a name="service-rest-api"></a>서비스 REST API

솔루션 백 엔드에서 [서비스 REST API](/rest/api/iothub/service/configuration)를 사용하여 디바이스를 관리할 수 있습니다. API를 사용하면 [디바이스 쌍](#device-twin) 속성을 검색 및 업데이트하고 [직접 메서드](#direct-method)를 호출하고 [작업](#job)을 예약할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [서비스 SDK](#azure-iot-service-sdks)를 사용해야 합니다.

### <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명(SAS)은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. SAS 인증에는 _공유 액세스 정책_ 과 _공유 액세스 서명_(토큰이라고 부름)의 두 구성 요소가 있습니다. 디바이스는 SAS를 사용하여 IoT Hub를 인증합니다. [백 엔드 앱](#back-end-app)도 SAS를 사용하여 IoT Hub에서 서비스 지향 엔드포인트를 인증합니다. 일반적으로 앱이 IoT Hub와 연결을 설정하기 위해 사용하는 [연결 문자열](#connection-string)에 SAS 토큰을 포함합니다.

### <a name="shared-access-policy"></a>공유 액세스 정책

공유 액세스 정책은 해당 정책과 관련된 유효한 [기본 또는 보조 키](#primary-and-secondary-keys)를 보유한 모든 사람에게 부여할 사용 권한을 정의합니다. 포털에서 허브에 대한 공유 액세스 정책 및 키를 관리할 수 있습니다.

### <a name="simulated-device"></a>시뮬레이션된 디바이스

편의를 위해 많은 IoT Hub 자습서에서 시뮬레이션된 디바이스를 사용하여 로컬 머신에서 샘플을 실행할 수 있도록 합니다. 반면에, [물리적 디바이스](#physical-device)는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 디바이스입니다.

### <a name="solution"></a>솔루션

_솔루션_ 은 하나 이상의 프로젝트를 포함하는 Visual Studio 솔루션을 나타낼 수 있습니다. _솔루션_ 은 또한 디바이스, [디바이스 앱](#device-app), IoT Hub, 기타 Azure 서비스 및 [백 엔드 앱](#back-end-app)과 같은 요소를 포함하는 IoT 솔루션을 나타낼 수 있습니다.

### <a name="solution-builder"></a>솔루션 빌더

솔루션 빌더는 솔루션 백 엔드를 만듭니다. 솔루션 빌더는 일반적으로 IoT Hub 및 [모델 리포지토리와](#model-repository)같은 Azure 리소스와 함께 작동 합니다.

### <a name="system-properties"></a>시스템 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 시스템 속성은 읽기 전용이며 마지막 작업 시간 및 연결 상태와 같은 디바이스 사용에 대한 정보를 포함합니다.

## <a name="t"></a>T

### <a name="tags"></a>태그

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 태그는 JSON 문서 형식으로 솔루션 백 엔드에 의해 저장 및 검색되는 디바이스 메타데이터입니다. 태그는 디바이스 앱에 표시되지 않습니다.

### <a name="telemetry"></a>원격 분석

디바이스는 풍속 또는 온도와 같은 원격 분석 데이터를 수집하고 데이터 요소 메시지를 사용하여 IoT Hub에 원격 분석을 보냅니다.

IoT 플러그 앤 플레이에서 [인터페이스](#interface) 에 정의 된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 데이터 스트림으로 보내는 센서 판독값과 같은 값입니다.

### <a name="token-service"></a>토큰 서비스

토큰 서비스를 사용하여 디바이스에 대한 인증 메커니즘을 구현합니다. **DeviceConnect** 권한으로 IoT Hub [공유 액세스 정책](#shared-access-policy)을 사용하여 *device-scoped* 토큰을 만듭니다. 이러한 토큰은 디바이스에서 IoT Hub에 연결할 수 있게 해줍니다. 디바이스는 사용자 지정 인증 메커니즘을 사용하여 토큰 서비스를 인증합니다. 디바이스가 성공적으로 인증되면 토큰 서비스는 사용할 디바이스에 대한 SAS 토큰을 발행하여 IoT Hub에 액세스합니다.

### <a name="twin-queries"></a>쌍 쿼리

[디바이스 및 모듈 쌍 쿼리](../iot-hub/iot-hub-devguide-query-language.md)에서는 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 디바이스 쌍 또는 모듈 쌍에서 정보를 검색합니다. 동일한 IoT Hub 쿼리 언어를 사용 하 여 IoT Hub에서 실행 되는 [작업](#job) 에 대 한 정보를 검색할 수 있습니다.

### <a name="twin-synchronization"></a>쌍 동기화

쌍 동기화는 디바이스 쌍 또는 모듈 쌍에서 [원하는 속성](#desired-properties)을 사용하여 디바이스 또는 모듈을 구성하고 쌍에 저장할 디바이스 또는 모듈의 [보고된 속성](#reported-properties)을 검색합니다.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X.509 클라이언트 인증서

디바이스는 X.509 인증서를 사용하여 [IoT Hub](#iot-hub)와 인증을 수행할 수 있습니다. [SAS 토큰](#shared-access-signature)을 사용하는 대신 X.509 인증서를 사용합니다.
