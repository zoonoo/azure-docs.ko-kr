---
title: Azure IoT Central 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 응용 프로그램에서 Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 1aac5af916e414178676a1caf42fead41109de68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974464"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Azure IoT Central 데이터 내보내기 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*이 항목은 관리자에 게 적용 됩니다.*

이 문서에서는 azure IoT Central의 연속 데이터 내보내기 기능을 사용 하 여 **azure Event Hubs**, **Azure Service Bus**또는 **azure Blob storage** 인스턴스로 데이터를 내보내는 방법을 설명 합니다. 데이터는 JSON 형식으로 내보내지고 원격 분석, 장치 정보 및 장치 템플릿 정보를 포함할 수 있습니다. 내보낸 데이터 사용:

- 웜 경로 정보 및 분석입니다. 이 옵션에는 Azure Stream Analytics의 사용자 지정 규칙 트리거, Azure Logic Apps에서 사용자 지정 워크플로 트리거 또는 변환할 Azure Functions를 통해 전달 하는 작업이 포함 됩니다.
- Microsoft Power BI에서 Azure Machine Learning 또는 장기적인 추세 분석의 학습 모델 등의 콜드 경로 분석.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 순간부터 데이터를 가져옵니다. 현재 연속 데이터 내보내기가 해제 된 시간에 대 한 데이터를 검색할 수 없습니다. 기록 데이터를 더 보존 하려면 연속 데이터 내보내기를 초기에 설정 합니다.

## <a name="prerequisites"></a>Előfeltételek

IoT Central 응용 프로그램의 관리자 여야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

연속 데이터 내보내기를 구성 하려면 먼저 내보내기 대상이 있어야 합니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

내보낼 기존 Event Hubs 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에 새 Event Hubs 네임 스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

2. Válasszon előfizetést. 종 량 제 IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Event Hubs 네임 스페이스에 이벤트 허브를 만듭니다. 네임 스페이스로 이동 하 고 맨 위에 있는 **+ 이벤트 허브** 를 선택 하 여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임 스페이스 만들기

내보낼 기존 Service Bus 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에 새 Service Bus 네임 스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.
2. Válasszon előfizetést. 종 량 제 IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Service Bus 네임 스페이스로 이동 하 고 맨 위에 있는 **+ queue** 또는 **+ 토픽** 을 선택 하 여 내보낼 큐 또는 항목을 만듭니다.

내보내기 대상으로 Service Bus를 선택 하는 경우 큐 및 항목에는 세션이 나 중복 검색이 설정 되어 있지 않아야 합니다. 이러한 옵션 중 하나를 사용 하도록 설정 하면 일부 메시지가 큐 또는 토픽에 도착 하지 않습니다.

### <a name="create-storage-account"></a>Storage-fiók létrehozása

로 내보낼 기존 Azure Storage 계정이 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 저장소 계정을](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)만듭니다. 새 [Azure Blob Storage 계정](https://aka.ms/blobdocscreatestorageaccount) 만들기 또는 [v2 저장소 계정 Azure Data Lake Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md)에 대해 자세히 알아볼 수 있습니다.

    - Azure Data Lake Storage v2 저장소 계정으로 데이터를 내보내도록 선택 하는 경우 **계정 종류**로 **blobstorage** 를 선택 해야 합니다.
    - 종 량 제 IoT Central 응용 프로그램에 대 한 것과 다른 구독에서 저장소 계정으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

2. 저장소 계정에서 컨테이너를 만듭니다. 저장소 계정으로 이동 합니다. **Blob Service**에서 **blob 찾아보기**를 선택 합니다. 위쪽에서 **+ 컨테이너** 를 선택 하 여 새 컨테이너를 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

이제 데이터를 내보낼 대상이 있으므로 다음 단계에 따라 연속 데이터 내보내기를 설정 합니다.

1. IoT Central 응용 프로그램에 로그인 합니다.

2. 왼쪽 창에서 **데이터 내보내기**를 선택 합니다.

    > [!Note]
    > 왼쪽 창에 데이터 내보내기가 표시 되지 않는 경우 앱의 관리자가 아닙니다. 데이터 내보내기를 설정 하려면 관리자에 게 문의 하세요.

3. 오른쪽 위에 있는 **+ 새로 만들기** 단추를 선택 합니다. **Azure Event Hubs**, **Azure Service Bus**또는 **azure Blob storage** 중 하나를 내보내기 대상으로 선택 합니다. 응용 프로그램당 최대 내보내기 수는 5 개입니다.

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data/export-new2.png)

4. 드롭다운 목록 상자에서 **Event Hubs 네임 스페이스**, **Service Bus 네임**스페이스, **저장소 계정 네임 스페이스**를 선택 하거나 **연결 문자열을 입력**합니다.

    - IoT Central 응용 프로그램과 동일한 구독에서 저장소 계정, Event Hubs 네임 스페이스 및 Service Bus 네임 스페이스도 볼 수 있습니다. 이 구독 외부의 대상으로 내보내려면 **연결 문자열 입력** 을 선택 하 고 5 단계를 참조 하세요.
    - 7 일 평가판 앱의 경우 연속 데이터 내보내기를 구성 하는 유일한 방법은 연결 문자열을 통하는 것입니다. 7 일 평가판 앱에는 연결 된 Azure 구독이 없습니다.

    ![새 이벤트 허브 만들기](media/howto-export-data/export-eh.png)

5. 필드 **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여 넣을 수 있는 새 상자가 표시 됩니다. 에 대 한 연결 문자열을 가져오려면 다음을 수행 합니다.
    - Event Hubs 또는 Service Bus Azure Portal의 네임 스페이스로 이동 합니다.
        - **설정**아래에서 **공유 액세스 정책** 을 선택 합니다.
        - 기본 **RootManageSharedAccessKey** 를 선택 하거나 새 기본 항목을 만듭니다.
        - 기본 또는 보조 연결 문자열을 복사 합니다.
    - 저장소 계정에서 Azure Portal의 저장소 계정으로 이동 합니다.
        - **설정**아래에서 **액세스 키** 를 선택 합니다.
        - Key1 연결 문자열 또는 key2 연결 문자열을 복사 합니다.

6. 드롭다운 목록 상자에서 이벤트 허브, 큐, 항목 또는 컨테이너를 선택 합니다.

7. **내보낼 데이터**에서 유형을 **On**으로 설정 하 여 내보낼 데이터 유형을 선택 합니다.

8. 연속 데이터 내보내기를 설정 하려면 **데이터 내보내기** 토글이 **on**으로 설정 되어 있는지 확인 합니다. Kattintson a **Mentés** gombra.

9. 몇 분 후에 데이터가 선택한 대상에 표시 됩니다.

## <a name="export-contents-and-format"></a>콘텐츠 내보내기 및 형식

내보낸 원격 분석 데이터에는 원격 분석 값 자체 뿐만 아니라 장치 IoT Central 전송 된 전체 메시지가 포함 되어 있습니다. 내보낸 장치 데이터에는 모든 장치의 속성 및 메타 데이터에 대 한 변경 내용이 포함 되 고, 내보낸 장치 템플릿에는 모든 장치 템플릿에 대 한 변경 내용이 포함 됩니다.

Event Hubs 및 Service Bus의 경우 데이터를 거의 실시간으로 내보냅니다. 데이터는 본문 속성에 있고 JSON 형식입니다 (예제는 아래 참조).

Blob Storage의 경우 데이터는 분당 한 번 내보내집니다. 각 파일은 마지막으로 내보낸 파일 이후의 변경 내용 일괄 처리를 포함 합니다. 내보낸 데이터는 JSON 형식으로 3 개의 폴더에 배치 됩니다. 저장소 계정의 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치 템플릿: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

파일로 이동 하 고 **Blob 편집** 탭을 선택 하 여 Azure Portal에서 내보낸 파일을 찾아볼 수 있습니다.


## <a name="telemetry"></a>Telemetria

Event Hubs 및 Service Bus의 경우 장치에서 메시지를 받은 IoT Central 후 새 메시지를 신속 하 게 내보내고, 내보낸 각 메시지에는 장치에서 JSON 형식의 body 속성으로 보낸 전체 메시지가 포함 됩니다.

Blob Storage의 경우 메시지가 일괄 처리 되 고 분당 한 번 내보내집니다. 내보낸 파일은 blob storage에 [IoT Hub 메시지 라우팅을](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) 통해 내보낸 메시지 파일과 동일한 형식을 사용 합니다. 

> [!NOTE]
> Blob Storage 장치에서 `contentType: application/JSON` 및 `contentEncoding:utf-8` (또는 `utf-16`, `utf-32`) 메시지를 보내고 있는지 확인 합니다. 예제는 [IoT Hub 설명서](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) 를 참조 하세요.

원격 분석을 전송 하는 장치는 장치 ID로 표시 됩니다 (다음 섹션 참조). 장치 이름을 가져오려면 장치 데이터를 내보내고 장치 메시지의 **deviceid** 와 일치 하는 **connectiondeviceid** 를 사용 하 여 각 메시지의 상관 관계를 확인 합니다.

이 메시지는 이벤트 허브 또는 Service Bus 큐 또는 토픽에서 받은 예제 메시지입니다.

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

Blob 저장소로 내보낸 예제 레코드는 다음과 같습니다.

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

## <a name="devices"></a>Eszközök

스냅숏의 각 메시지 또는 레코드는 마지막으로 내보낸 메시지 이후 장치 및 해당 속성에 대 한 변경 내용을 하나 이상 나타냅니다. Az érintett műveletek közé tartoznak az alábbiak:

- 장치 `@id` IoT Central
- 장치 `name`
- [장치 프로 비전 서비스](../core/howto-connect-nodejs.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) 에서 `deviceId`
- 장치 템플릿 정보
- 속성 값

각 장치가 속한 장치 템플릿은 `instanceOf`표시 됩니다. 장치 템플릿에 대 한 이름 및 추가 정보를 가져오려면 장치 템플릿 데이터를 내보내야 합니다.

삭제 된 장치는 내보내지 않습니다. 현재는 삭제 된 장치에 대해 내보낸 메시지에 표시기가 없습니다.

Event Hubs 및 Service Bus의 경우 장치 데이터를 포함 하는 메시지는 IoT Central에 표시 되는 거의 실시간으로 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송 됩니다. 

Blob Storage의 경우 마지막으로 쓴 이후의 모든 변경 내용을 포함 하는 새 스냅숏은 분당 한 번 내보냅니다.

다음은 event hub 또는 Service Bus 큐 또는 토픽의 장치 및 속성 데이터에 대 한 예제 메시지입니다.

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

Blob Storage의 장치 및 속성 데이터를 포함 하는 예제 스냅숏입니다. 내보낸 파일은 레코드 당 한 줄을 포함 합니다.

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

## <a name="device-templates"></a>Eszközsablonok

각 메시지 또는 스냅숏 레코드는 마지막으로 내보낸 메시지 이후 장치 템플릿에 대 한 변경 내용을 하나 이상 나타냅니다. 각 메시지 또는 레코드에서 전송 되는 정보는 다음과 같습니다.

- 위의 장치 스트림의 `instanceOf`와 일치 하는 장치 템플릿의 `@id`
- 장치 템플릿의 `name`
- 장치 템플릿의 `version`
- 장치 `capabilityModel` `interfaces`, 원격 분석, 속성 및 명령 정의를 포함 합니다.
- `cloudProperties` 정의
- 재정의 및 초기 값, `capabilityModel` 인라인

삭제 된 장치 템플릿은 내보내지지 않습니다. 현재는 삭제 된 장치 템플릿에 대해 내보낸 메시지에 표시기가 없습니다.

Event Hubs 및 Service Bus의 경우 장치 템플릿 데이터가 포함 된 메시지는 IoT Central에 표시 되는 거의 실시간으로 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송 됩니다. 

Blob Storage의 경우 마지막으로 쓴 이후의 모든 변경 내용을 포함 하는 새 스냅숏은 분당 한 번 내보냅니다.

다음은 이벤트 허브의 장치 템플릿 데이터 또는 Service Bus 큐 또는 토픽에 대 한 예제 메시지입니다.

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

Blob Storage의 장치 및 속성 데이터를 포함 하는 예제 스냅숏입니다. 내보낸 파일은 레코드 당 한 줄을 포함 합니다.

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

## <a name="next-steps"></a>Következő lépések

이제 Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage으로 데이터를 내보내는 방법을 배웠으므로 다음 단계를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [Azure Functions 트리거하는 방법](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
