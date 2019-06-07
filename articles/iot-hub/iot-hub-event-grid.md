---
title: Azure IoT Hub 및 Azure Event Grid | Microsoft Docs
description: IoT Hub에서 발생하는 작업을 기반으로 프로세스를 트리거하려면 Azure Event Grid를 사용합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 73a9aebfd0c5338f63927860ce3f6c57b20428a4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754780"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응

Azure IoT Hub는 이벤트 알림을 다른 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합됩니다. 안전하고 안정적이며 확장성 있는 방식으로 중요한 이벤트에 대응할 수 있도록 IoT Hub 이벤트를 수신 대기 할 수 있게 비즈니스 애플리케이션을 구성합니다. 예를 들어, 데이터베이스를 업데이트, 작업 티켓을 만들고, IoT hub에 새 IoT 장치가 등록 될 때마다 전자 메일 알림을 제공 하는 응용 프로그램을 빌드하십시오.

[Azure Event Grid](../event-grid/overview.md)는 게시-구독 모델을 사용하여 완전히 관리되는 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

![Azure Event Grid 아키텍처](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>국가별 가용성

Event Grid가지 원되는 지역의 IoT Hub에 대해 Event Grid 통합을 사용할 수 있습니다. 장치 원격 분석 이벤트를 제외한 모든 장치 이벤트는 일반적으로 사용할 수 있습니다. 장치 원격 분석 이벤트 공개 미리 보기로 제공 되며는 미국 동부, 미국 서 부, 유럽 서 부를 제외한 모든 지역에서 제공 됩니다 [Azure Government](/azure/azure-government/documentation-government-welcome)하십시오 [Azure 중국 21Vianet](/azure/china), 및 [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). 최신 지역 목록을 보려면 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

## <a name="event-types"></a>일정 유형

IoT Hub는 다음과 같은 이벤트 유형을 게시합니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 디바이스를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 디바이스를 삭제하는 경우 게시합니다. |
| Microsoft.Devices.DeviceConnected | IoT Hub에 디바이스가 연결되는 경우 게시합니다. |
| Microsoft.Devices.DeviceDisconnected | IoT Hub와 디바이스의 연결이 해제되는 경우 게시합니다. |
| Microsoft.Devices.DeviceTelemetry | IoT hub에 장치 원격 분석 메시지를 보내는 경우 게시 |

Azure Portal 또는 Azure 명령줄 인터페이스를 사용하여 각 IoT 허브에서 어떤 이벤트를 게시할지 구성할 수 있습니다. 한 예로 자습서 [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](../event-grid/publish-iot-hub-events-to-logic-apps.md)를 시도해 봅니다.

## <a name="event-schema"></a>이벤트 스키마

IoT Hub 이벤트에는 디바이스 수명 주기 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. EventType 속성이 **Microsoft.Devices**로 시작하는지 확인하면 IoT Hub 이벤트인지 식별할 수 있습니다. Event Grid 이벤트 속성을 사용하는 방법에 대한 자세한 내용은 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.

### <a name="device-connected-schema"></a>디바이스 연결됨 스키마

다음 예제는 디바이스 연결됨 이벤트의 스키마를 보여 줍니다.

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>장치 원격 분석 스키마

JSON으로 설정 된 contentType 및 contentEncoding 메시지에서 u t F-8로 사용 하 여 장치 원격 분석 메시지 유효한 JSON 형식 이어야 합니다 [시스템 속성](iot-hub-devguide-routing-query-syntax.md#system-properties)합니다. 이 설정 하지 않으면 IoT Hub 기본 64로 인코딩된 형식으로 메시지를 작성 합니다.

Event Grid로 끝점을 선택 하 여 Event Grid에 게시 되기 전에 장치 원격 분석 이벤트를 보강할 수 있습니다. 자세한 내용은 [메시지 원칙이 개요](iot-hub-message-enrichments-overview.md)합니다.

다음 예제에서는 장치 원격 분석 이벤트의 스키마를 보여줍니다.

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>스키마를 생성한 디바이스

다음 예제는 디바이스가 생성된 이벤트의 스키마를 보여줍니다.

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

각 속성에 대 한 자세한 설명을 참조 하세요 [IoT Hub에 대 한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-iot-hub.md)합니다.

## <a name="filter-events"></a>이벤트 필터링

IoT Hub 이벤트 구독은 이벤트 유형, 데이터 콘텐츠 및 제목, 장치 이름 기준으로 이벤트를 필터링 수 있습니다.

이벤트 표를 사용 하면 [필터링](../event-grid/event-filtering.md) 이벤트 형식, 주제 및 데이터 콘텐츠입니다. Event Grid 구독을 만드는 동안 선택한 IoT 이벤트를 구독할 수 있습니다. Event Grid의 제목 필터는 **시작 문자**(접두사) 및 **끝 문자**(접미사) 일치를 기반으로 작동합니다. 필터는 `AND` 연산자를 사용하므로 접두사와 접미사가 둘 다 일치하는 제목의 이벤트가 구독자에게 전달됩니다.

IoT 이벤트의 제목은 다음 형식을 사용합니다.

```json
devices/{deviceId}
```

데이터 콘텐츠를 포함 하는 각 이벤트의 특성 필터링에 대 한 event Grid 수도 있습니다. 이 통해 이벤트 기반된 원격 분석 메시지 내용을 전달할지를 선택할 수 있습니다. 참조 하세요 [고급 필터링](../event-grid/event-filtering.md#advanced-filtering) 예제를 보려면.

DeviceConnected, DeviceDisconnected, DeviceCreated 및 DeviceDeleted 같은 비-원격 분석 이벤트에 대 한 Event Grid를 필터링 수 사용할 구독을 만들 때. Event Grid에서 필터링 하는 것 외에도 원격 분석 이벤트에 대 한 사용자 장치 쌍, 메시지 속성 및 메시지 라우팅 쿼리 본문에서 필터링 수도 있습니다. 기본값을 만들겠습니다 [경로](iot-hub-devguide-messages-d2c.md) IoT Hub에서 장치 원격 분석에 Event Grid 구독에 따라 합니다. 이 단일 경로 모든 Event Grid 구독을 처리할 수 있습니다. 원격 분석 데이터를 보내기 전에 메시지를 필터링, 업데이트할 수 있습니다 하 [라우팅 쿼리](iot-hub-devguide-routing-query-syntax.md)합니다. 라우팅 쿼리 하는 본문은 JSON 하는 경우에 메시지 본문에 적용할 수 있습니다 note 합니다.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>디바이스 연결됨 및 디바이스 연결 끊김 이벤트에 대한 제한

디바이스 연결됨 및 디바이스 연결 끊김 이벤트를 받으려면 디바이스에 대한 C2D 링크 또는 D2C 링크를 열어야 합니다. 디바이스가 MQTT 프로토콜을 사용하는 경우, IoT Hub는 C2D 링크를 열어둡니다. AMQP에 대 한 호출 하 여 C2D 링크를 열 수를 [비동기 API 수신](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)합니다.

원격 분석 데이터를 보내는 경우 D2C 링크가 열려 있습니다. 장치 연결 깜박이 경우 즉, 장치 연결 및 자주 연결에서는 모든 단일 연결 상태를 보내지 않습니다 하지만 1 분 마다 스냅숏을는 연결 상태를 게시 합니다. IoT Hub가 중단되는 경우에는 중단이 끝나는 즉시 디바이스 연결 상태를 게시합니다. 중단된 상태에서 디바이스 연결이 끊기면 디바이스 연결 끊김 이벤트가 10분 내에 게시됩니다.

## <a name="tips-for-consuming-events"></a>이벤트 사용하기 위한 팁

IoT Hub 이벤트를 처리하는 애플리케이션은 다음 권장 사항을 따라야 합니다.

* 특정 원본에서 이벤트는 가정 하지 마십시오 하므로 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독을 구성할 수 있습니다. 항상 메시지 항목을 확인하여 예상한 IoT 허브에서 온 개체인지 확인합니다.

* 받은 모든 이벤트가 예상대로의 형식일 것이라고 가정하지 마십시오. 메시지를 처리하기 전에 eventType을 항상 확인합니다.

* 메시지는 지연 후 또는 잘못된 순서로 도착할 수 있습니다. 개체에 대 한 정보 생성 하는 장치 또는 장치 삭제 이벤트에 대 한 최신 상태 인지 알아보려면 etag 필드를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [IoT Hub 이벤트 자습서를 시도해 봅니다.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [디바이스 연결 및 연결 해제 이벤트를 정렬하는 방법](iot-hub-how-to-order-connection-state-events.md)에 대해 알아봅니다.

* [Event Hubs에 대한 자세한 정보](../event-grid/overview.md)

* [IoT Hub 이벤트 라우팅 및 메시지 간의 차이점 비교](iot-hub-event-grid-routing-comparison.md)
