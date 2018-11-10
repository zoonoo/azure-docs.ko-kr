---
title: Azure Digital Twins의 송신 및 엔드포인트 | Microsoft Docs
description: Azure Digital Twins를 사용하여 엔드포인트를 만드는 방법에 대한 지침
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212253"
---
# <a name="egress-and-endpoints"></a>송신 및 엔드포인트

Azure Digital Twins는 각 엔드포인트가 사용자의 Azure 구독의 메시지/이벤트 브로커를 나타내는 ‘엔드포인트’ 개념을 지원합니다. 이벤트 및 메시지는 **이벤트 허브**, **Event Grid** 및 **Service Bus 항목**에 전송할 수 있습니다.

사전 정의된 라우팅 기본 설정에 따라 이벤트가 엔드포인트로 전송됩니다. 사용자는 **TopologyOperation**, **UdfCustom**, **SensorChange**, **SpaceChange** 또는 **DeviceMessage** 이벤트 중 하나를 수신해야 하는 엔드포인트를 지정할 수 있습니다.

이벤트 라우팅 및 이벤트 유형에 대해 기본적으로 이해하려면 [이벤트 라우팅 및 메시지](concepts-events-routing.md)를 참조하세요.

## <a name="event-types-description"></a>이벤트 유형 설명

다음은 각 이벤트 유형의 이벤트 형식입니다.

- **TopologyOperation**

  그래프 변경 내용에 적용됩니다. *subject* 속성은 영향받는 개체의 유형을 지정합니다. 이 이벤트를 트리거할 수 있는 개체의 유형은 **Device**, **DeviceBlobMetadata**, **DeviceExtendedProperty**, **ExtendedPropertyKey**, **ExtendedType**, **KeyStore**, **Report**, **RoleDefinition**, **Sensor**, **SensorBlobMetadata**, **SensorExtendedProperty**, **Space**,  **SpaceBlobMetadata**, **SpaceExtendedProperty**, **SpaceResource**, **SpaceRoleAssignment**, **System**, **User**, **UserBlobMetadata**, **UserExtendedProperty**입니다.

  예제:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **UdfCustom**

  UDF(사용자 정의 함수)에서 전송하는 이벤트입니다. 
  
  > [!IMPORTANT]
  > 이 이벤트는 명시적으로 UDF 자체에서 전송해야 합니다.

  예제:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **SensorChange**

  원격 분석 변경 내용을 기반으로 한 센서의 상태 업데이트입니다.

  예제:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **SpaceChange**

  원격 분석 변경 내용을 기반으로 한 공간의 상태 업데이트입니다.

  예제:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **DeviceMessage**

  Azure Digital Twins에서 원시 원격 분석 이벤트를 라우팅할 수 있는 **EventHub** 연결을 지정할 수 있습니다.

> [!NOTE]
> - **DeviceMessage**는 **EventHub**와만 조합할 수 있으며, **DeviceMessage**를 다른 이벤트 유형과 조합할 수 없습니다.
> - **EventHub** 또는 **DeviceMessage** 유형의 조합에 대해 하나의 엔드포인트만 지정할 수 있습니다.

## <a name="configuring-endpoints"></a>엔드포인트 구성

엔드포인트 관리는 엔드포인트 API를 통해 수행합니다. 다음은 지원되는 다른 엔드포인트를 구성하는 방법에 대한 몇 가지 예입니다. 엔드포인트에 대한 라우팅을 정의하므로 이벤트 유형 배열에 특히 주의하세요.

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- **Service Bus** 이벤트 유형 **SensorChange**, **SpaceChange**, **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourNamespace* | 엔드포인트의 네임스페이스 |
    | *yourPrimaryKey* | 인증에 사용되는 기본 연결 문자열 |
    | *yourSecondaryKey* | 인증에 사용되는 보조 연결 문자열 |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **Event Grid** 이벤트 유형 **SensorChange**, **SpaceChange**, **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourPrimaryKey* | 인증에 사용되는 기본 연결 문자열|
    | *yourSecondaryKey* | 인증에 사용되는 보조 연결 문자열 |
    | *yourTopicName* | 사용자 지정 항목의 이름 |

- **이벤트 허브** 이벤트 유형 **SensorChange**, **SpaceChange**, **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourNamespace* | 엔드포인트의 네임스페이스 |
    | *yourPrimaryKey* | 인증에 사용되는 기본 연결 문자열 |
    | *yourSecondaryKey* | 인증에 사용되는 보조 연결 문자열 |
    | *yourEventHubName* | **이벤트 허브**의 이름 |

- **이벤트 허브** 이벤트 유형 **DeviceMessage**로 라우팅합니다. `EntityPath`를 **connectionString**에 반드시 포함해야 합니다.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | 사용자 지정 특성 이름 | 바꿀 항목 |
    | --- | --- |
    | *yourNamespace* | 엔드포인트의 네임스페이스 |
    | *yourPrimaryKey* | 인증에 사용되는 기본 연결 문자열 |
    | *yourSecondaryKey* | 인증에 사용되는 보조 연결 문자열 |
    | *yourEventHubName* | **이벤트 허브**의 이름 |

> [!NOTE]
> 새 엔드포인트를 만들면 이 엔드포인트에서 이벤트 수신을 시작하는 데 최대 5~10분이 걸릴 수 있습니다.

## <a name="primary-and-secondary-connection-keys"></a>기본 및 보조 연결 키

기본 연결 키가 승인되지 않으면 시스템에서 자동으로 보조 연결 키를 시도합니다. 이렇게 하면 백업이 제공되고 엔드포인트 API를 통해 기본 키를 유연하게 인증 및 업데이트할 수 있습니다.

기본 및 보조 연결 키가 모두 승인되지 않은 경우 시스템은 최대 30분의 지수 백오프 대기 시간을 입력합니다. 트리거된 각 백오프 대기 시간에 이벤트가 삭제됩니다.

시스템이 백오프 대기 상태일 때마다 엔드포인트 API를 통해 연결 키를 업데이트하면 변경 사항이 적용되는 데 최대 30분이 걸릴 수 있습니다.

## <a name="unreachable-endpoints"></a>연결할 수 없는 엔드포인트

엔드포인트에 연결할 수 없으면 시스템은 최대 30분의 지수 백오프 대기 시간을 입력합니다. 트리거된 각 백오프 대기 시간에 이벤트가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins Swagger를 사용하는 방법을 알아보려면 [Azure Digital Twins Swagger](how-to-use-swagger.md)를 참조하세요.

Azure Digital Twins의 라우팅 이벤트 및 메시지에 대해 자세히 알아보려면 [이벤트 라우팅 및 메시지](concepts-events-routing.md)를 참조하세요.
