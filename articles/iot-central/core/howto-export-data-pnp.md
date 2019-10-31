---
title: Azure IoT Central 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 응용 프로그램에서 Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 118d2b42bc14a943aa3fa60b34aa1c151d5dea4c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176297"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Azure IoT Central 데이터 내보내기 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 azure IoT Central의 연속 데이터 내보내기 기능을 사용 하 여 Azure Event Hubs, Azure Service Bus 또는 Azure Blob storage 인스턴스로 데이터를 내보내는 방법을 설명 합니다. 데이터는 JSON 형식으로 내보내지고 원격 분석, 장치 정보 및 장치 템플릿 정보를 포함할 수 있습니다. 내보낸 데이터 사용:

- 웜 경로 정보 및 분석입니다. 이 옵션에는 Azure Stream Analytics의 사용자 지정 규칙 트리거, Azure Logic Apps에서 사용자 지정 워크플로 트리거 또는 변환할 Azure Functions를 통해 전달 하는 작업이 포함 됩니다.
- Microsoft Power BI에서 Azure Machine Learning 또는 장기적인 추세 분석의 학습 모델 등의 콜드 경로 분석.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.

## <a name="prerequisites"></a>전제 조건

IoT Central 애플리케이션에서 관리자여야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

연속 데이터 내보내기를 구성 하려면 먼저 내보내기 대상이 있어야 합니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

내보낼 기존 Event Hubs 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다. 종 량 제 IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브**를 선택하여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

내보낼 기존 Service Bus 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에 새 Service Bus 네임 스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.
2. 구독을 선택합니다. 종 량 제 IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Service Bus 네임스페이스로 이동한 다음, 맨 위에서 **+ 큐** 또는 **+ 토픽**을 선택하여 내보낼 큐 또는 토픽을 만듭니다.

내보내기 대상으로 Service Bus를 선택 하는 경우 큐 및 항목에는 세션이 나 중복 검색이 설정 되어 있지 않아야 합니다. 이러한 옵션 중 하나가 사용하도록 설정되면 일부 메시지가 큐 또는 토픽에 도착하지 않습니다.

### <a name="create-storage-account"></a>스토리지 계정 만들기

로 내보낼 기존 Azure Storage 계정이 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob Storage 계정](https://aka.ms/blobdocscreatestorageaccount) 만들기 또는 [v2 저장소 계정 Azure Data Lake Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md)에 대해 자세히 알아볼 수 있습니다.

    - Azure Data Lake Storage v2 저장소 계정으로 데이터를 내보내도록 선택 하는 경우 **계정 종류**로 **blobstorage** 를 선택 해야 합니다.
    - 종 량 제 IoT Central 응용 프로그램에 대 한 것과 다른 구독에서 저장소 계정으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

이제 데이터를 내보낼 대상이 있으므로 다음 단계에 따라 연속 데이터 내보내기를 설정 합니다.

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽 창에서 **데이터 내보내기**를 선택 합니다.

    > [!Note]
    > 왼쪽 창에 데이터 내보내기가 표시 되지 않는 경우 앱의 관리자가 아닙니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. 오른쪽 위에 있는 **+ 새로 만들기** 단추를 선택 합니다. **Azure Event Hubs**, **Azure Service Bus**또는 **azure Blob storage** 중 하나를 내보내기 대상으로 선택 합니다. 응용 프로그램당 최대 내보내기 수는 5 개입니다.

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data-pnp/export-new2.png)

4. 드롭다운 목록 상자에서 **Event Hubs 네임 스페이스**, **Service Bus 네임**스페이스, **저장소 계정 네임 스페이스**를 선택 하거나 **연결 문자열을 입력**합니다.

    - IoT Central 응용 프로그램과 동일한 구독에서 저장소 계정, Event Hubs 네임 스페이스 및 Service Bus 네임 스페이스도 볼 수 있습니다. 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.
    - 7 일 평가판 앱의 경우 연속 데이터 내보내기를 구성 하는 유일한 방법은 연결 문자열을 통하는 것입니다. 7 일 평가판 앱에는 연결 된 Azure 구독이 없습니다.

    ![새 이벤트 허브 만들기](media/howto-export-data-pnp/export-eh.png)

5. (선택 사항) **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 다음 항목의 연결 문자열을 가져오려면
    - Event Hubs 또는 Service Bus Azure Portal의 네임 스페이스로 이동 합니다.
        - **설정**아래에서 **공유 액세스 정책** 을 선택 합니다.
        - 기본 **RootManageSharedAccessKey**를 선택하거나 새로 만듭니다.
        - 주 또는 보조 연결 문자열 중 하나를 복사합니다.
    - 저장소 계정에서 Azure Portal의 저장소 계정으로 이동 합니다.
        - **설정**아래에서 **액세스 키** 를 선택 합니다.
        - Key1 연결 문자열 또는 key2 연결 문자열을 복사 합니다.

6. 드롭다운 목록 상자에서 이벤트 허브, 큐, 항목 또는 컨테이너를 선택 합니다.

7. **내보낼 데이터**에서 유형을 **On**으로 설정 하 여 내보낼 데이터 유형을 선택 합니다.

8. 연속 데이터 내보내기를 설정 하려면 **데이터 내보내기** 토글이 **on**으로 설정 되어 있는지 확인 합니다. **저장**을 선택합니다.

9. 몇 분 후에 데이터가 선택한 대상에 표시 됩니다.

## <a name="data-format"></a>데이터 형식

데이터는 거의 실시간으로 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 내보내집니다.

데이터는 1 분 마다 한 번씩 저장소 계정으로 내보내집니다. 각 파일은 마지막으로 내보낸 파일 이후의 변경 내용 일괄 처리를 포함 합니다. 내보낸 데이터는 JSON 형식으로 3 개의 폴더에 배치 됩니다. 스토리지 계정의 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치 템플릿: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

파일로 이동 하 고 **Blob 편집** 탭을 선택 하 여 Azure Portal에서 내보낸 파일을 찾아볼 수 있습니다.

내보낸 원격 분석 데이터에는 원격 분석 값 자체 뿐만 아니라 장치 IoT Central 전송 된 전체 메시지가 포함 되어 있습니다. 내보낸 장치 데이터에는 모든 장치의 속성 및 메타 데이터에 대 한 변경 내용이 포함 되 고, 내보낸 장치 템플릿에는 모든 장치 템플릿에 대 한 변경 내용이 포함 됩니다. 내보낸 데이터는 body 속성 이며 JSON 형식입니다.

### <a name="telemetry"></a>원격 분석

IoT Central이 디바이스에서 메시지를 받은 후 빠르게 새 메시지를 내보냅니다.

- Event Hubs 및 Service Bus에서 내보낸 각 메시지에는 장치를 JSON 형식의 body 속성으로 보낸 전체 메시지가 포함 되어 있습니다.
- Blob 저장소에서 내보낸 파일은 blob 저장소에 [IoT Hub 메시지 라우팅을](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) 통해 내보낸 메시지 파일과 동일한 형식을 사용 합니다. 장치가 `contentType: application/JSON` 하 고 `contentEncoding:utf-8` (또는 `utf-16``utf-32`) 메시지를 보내고 있는지 확인 합니다. 예제는 [IoT Hub 설명서](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) 를 참조 하세요.

원격 분석을 보내는 장치는 장치 Id로 표시 됩니다 (다음 섹션 참조). 장치 이름을 가져오려면 장치 데이터를 내보내고 장치 메시지의 **deviceid** 와 일치 하는 **connectiondeviceid** 를 사용 하 여 각 메시지의 상관 관계를 확인 합니다.

다음 예에서는 이벤트 허브 또는 Service Bus 큐 또는 토픽에서 받은 원격 분석 데이터에 대 한 메시지를 보여 줍니다.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

다음 예제에서는 blob 저장소에 있는 JSON 형식의 레코드를 보여 줍니다.

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>디바이스

디바이스 데이터를 포함하는 메시지는 몇 분마다 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송됩니다. 새 스냅숏은 분당 한 번 blob 저장소에 기록 됩니다. 각 메시지 또는 스냅숏에는 다음에 대 한 데이터가 포함 됩니다.

- 추가된 새 디바이스
- 변경 된 속성 값이 있는 장치

스냅숏의 각 메시지 또는 레코드는 마지막으로 내보낸 메시지 이후 장치에 대 한 변경 내용을 하나 이상 나타냅니다. 정보는 다음과 같습니다.

- IoT Central의 디바이스 `@id`
- 디바이스의 `name`
- [Device Provisioning Service](/azure/iot-central/core/howto-connect-nodejs)의 `deviceId`
- 디바이스 템플릿 정보
- 속성 값

마지막 배치 이후에 삭제된 디바이스는 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스에 대한 표시기가 없습니다.

각 디바이스가 속하는 디바이스 템플릿은 디바이스 템플릿 ID로 표시됩니다. 디바이스 템플릿의 이름을 가져오려면 디바이스 템플릿 데이터도 내보내야 합니다.

다음 예제는 이벤트 허브 또는 Service Bus 큐 또는 토픽에 있는 디바이스 데이터 관련 메시지를 보여 줍니다.

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

내보낸 파일은 레코드 당 한 줄을 포함 합니다. 다음 예제에서는 JSON 형식의 레코드를 보여 줍니다.

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>디바이스 템플릿

디바이스 템플릿 데이터를 포함하는 메시지는 몇 분마다 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송됩니다. 새 스냅숏은 blob 저장소에 분당 한 번 작성 됩니다. 따라서 몇 분 마다 메시지 일괄 처리에 대 한 데이터가 도착 합니다.

장치 템플릿 데이터를 포함 하는 메시지는 몇 분 마다 한 번씩 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송 됩니다. 새 스냅숏은 분당 한 번 blob 저장소에 기록 됩니다. 각 메시지 또는 스냅숏에는 다음에 대 한 데이터가 포함 됩니다.

- 추가 되었거나 버전이 지정 된 새 장치 템플릿
- 변경 된 기능 모델, 클라우드 속성, 재정의 및 초기 값이 있는 장치 템플릿

각 메시지 또는 스냅숏 레코드는 마지막으로 내보낸 메시지 이후 장치 템플릿에 대 한 변경 내용을 하나 이상 나타냅니다. 각 메시지 또는 레코드에서 전송 되는 정보는 다음과 같습니다.

- 디바이스 템플릿의 `@id`
- 디바이스 템플릿의 `name`
- 디바이스 템플릿의 `version`
- 장치 `capabilityModel` `interfaces`, 원격 분석, 속성 및 명령 정의를 포함 합니다.
- `cloudProperties` 정의
- 재정의 및 초기 값, `capabilityModel` 인라인

마지막 배치 이후에 삭제된 디바이스 템플릿은 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스 템플릿에 대한 표시기가 없습니다.

다음 예제에서는 이벤트 허브 또는 Service Bus 큐 또는 토픽의 장치 템플릿 메시지를 보여 줍니다.

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

내보낸 파일은 레코드 당 한 줄을 포함 합니다. 다음 예제에서는 JSON 형식의 레코드를 보여 줍니다.

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

이제 Azure Event Hubs 또는 Azure Service Bus에 데이터를 내보내는 방법을 알아보았으므로 다음 단계를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Functions 트리거 방법](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
