---
title: Azure Event Hubs 및 Azure Service Bus에 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 애플리케이션에서 Azure Event Hubs 및 Azure Service Bus로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318434"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central에서 데이터 내보내기

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 Azure IoT Central의 연속 데이터 내보내기 기능을 사용하여 고유한 **Azure Event Hubs** 및 **Azure Service Bus** 인스턴스에 데이터를 내보내는 방법을 자세히 설명합니다. 웜 경로 인사이트 및 분석을 위해 고유한 대상에 **측정값**, **디바이스** 및 **디바이스 템플릿**을 내보낼 수 있습니다. 여기에는 Azure Stream Analytics에서 사용자 지정 규칙을 트리거하거나, Azure Logic Apps에서 사용자 지정 워크플로를 트리거하거나, Azure Functions를 통해 데이터를 변환 및 전달하는 작업이 포함됩니다. 

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.


## <a name="prerequisites"></a>필수 조건

- IoT Central 애플리케이션에서 관리자여야 함

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Azure Event Hubs 및 Azure Service Bus로 내보내기

측정값, 디바이스 및 디바이스 템플릿 데이터는 이벤트 허브, Service Bus 큐 또는 토픽으로 거의 실시간으로 내보냅니다. 내보낸 측정값 데이터에는 측정값 자체의 값뿐만 아니라 디바이스가 IoT Central에 보낸 전체 메시지가 포함됩니다. 내보낸 디바이스 데이터에는 모든 디바이스의 속성 및 설정 변경 내용이 포함되고, 디바이스 템플릿에는 모든 디바이스 템플릿의 변경 내용이 포함됩니다. 내보낸 데이터는 “body” 속성에 포함되고 JSON 형식입니다.

> [!NOTE]
> Service Bus를 내보내기 대상으로 선택할 경우 큐 및 토픽에서 **세션 또는 중복 검색이 사용하도록 설정되지 않아야 합니다**. 이러한 옵션 중 하나가 사용하도록 설정되면 일부 메시지가 큐 또는 토픽에 도착하지 않습니다.

### <a name="measurements"></a>측정값

IoT Central이 디바이스에서 메시지를 받은 후 빠르게 새 메시지를 내보냅니다. Event Hubs 및 Service Bus에 있는 각 내보낸 메시지에는 JSON 형식의 “body” 속성으로 디바이스가 보낸 전체 메시지가 포함됩니다. 

> [!NOTE]
> 측정값을 보내는 디바이스는 디바이스 ID로 표시됩니다(아래 섹션 참조). 디바이스의 이름을 가져오려면 디바이스 데이터를 내보내고 디바이스 메시지의 **deviceId**와 일치하는 **connectionDeviceId**를 사용하여 각 메시지의 상관 관계를 지정합니다.

다음 예제는 이벤트 허브 또는 Service Bus 큐 또는 토픽에서 수신된 측정값 데이터 관련 메시지를 보여 줍니다.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>디바이스

디바이스 데이터를 포함하는 메시지는 몇 분마다 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송됩니다. 이는 몇 분마다 메시지 배치가 데이터와 함께 도착함을 의미합니다.
- 추가된 새 디바이스
- 속성 및 설정 값이 변경된 디바이스

각 메시지는 마지막으로 내보낸 메시지 이후 적용된 하나 이상의 디바이스 변경 내용을 나타냅니다. 각 메시지로 전송되는 정보는 다음과 같습니다.
- IoT Central의 디바이스 `id`
- 디바이스의 `name`
- [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)의 `deviceId`
- 디바이스 템플릿 정보
- 속성 값
- 설정 값

> [!NOTE]
> 마지막 배치 이후에 삭제된 디바이스는 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스에 대한 표시기가 없습니다.
>
> 각 디바이스가 속하는 디바이스 템플릿은 디바이스 템플릿 ID로 표시됩니다. 디바이스 템플릿의 이름을 가져오려면 디바이스 템플릿 데이터도 내보내야 합니다.

다음 예제는 이벤트 허브 또는 Service Bus 큐 또는 토픽에 있는 디바이스 데이터 관련 메시지를 보여 줍니다.


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>디바이스 템플릿

디바이스 템플릿 데이터를 포함하는 메시지는 몇 분마다 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송됩니다. 이는 몇 분마다 메시지 배치가 데이터와 함께 도착함을 의미합니다.
- 추가된 새 디바이스 템플릿
- 측정값, 속성 및 설정 정의가 변경된 디바이스 템플릿

각 메시지는 마지막으로 내보낸 메시지 이후 적용된 하나 이상의 디바이스 템플릿 변경 내용을 나타냅니다. 각 메시지로 전송되는 정보는 다음과 같습니다.
- 디바이스 템플릿의 `id`
- 디바이스 템플릿의 `name`
- 디바이스 템플릿의 `version`
- 측정 데이터 형식 및 최솟/최댓값
- 속성 데이터 형식 및 기본값
- 설정 데이터 형식 및 기본값

> [!NOTE]
> 마지막 배치 이후에 삭제된 디바이스 템플릿은 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스 템플릿에 대한 표시기가 없습니다.

다음 예제는 이벤트 허브 또는 Service Bus 큐 또는 토픽에 있는 디바이스 템플릿 데이터 관련 메시지를 보여 줍니다.

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>다음 단계

이제 Azure Event Hubs 또는 Azure Service Bus에 데이터를 내보내는 방법을 알아보았으므로 다음 단계를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Functions 트리거 방법](howto-trigger-azure-functions.md)
