---
title: Azure IoT Central 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 응용 프로그램에서 Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f8b1364f24659f63ef537e69241e1468abbd0838
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988773"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Azure IoT Central 데이터 내보내기 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 azure IoT Central의 연속 데이터 내보내기 기능을 사용 하 여 **azure Event Hubs**, **Azure Service Bus**또는 **azure Blob storage** 인스턴스로 데이터를 내보내는 방법을 설명 합니다. 데이터는 JSON 형식으로 내보내지고 원격 분석, 장치 정보 및 장치 템플릿 정보를 포함할 수 있습니다. 내보낸 데이터 사용:

- 웜 경로 정보 및 분석입니다. 이 옵션에는 Azure Stream Analytics의 사용자 지정 규칙 트리거, Azure Logic Apps에서 사용자 지정 워크플로 트리거 또는 변환할 Azure Functions를 통해 전달 하는 작업이 포함 됩니다.
- Microsoft Power BI에서 Azure Machine Learning 또는 장기적인 추세 분석의 학습 모델 등의 콜드 경로 분석.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.

## <a name="prerequisites"></a>필수 조건

IoT Central 응용 프로그램의 관리자 이거나 데이터 내보내기 권한이 있어야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

연속 데이터 내보내기를 구성 하려면 먼저 내보내기 대상이 있어야 합니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

내보낼 기존 Event Hubs 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다. IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브**를 선택하여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

내보낼 기존 Service Bus 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에 새 Service Bus 네임 스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.
2. 구독을 선택합니다. IoT Central 응용 프로그램과 동일한 구독에 있지 않은 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Service Bus 네임스페이스로 이동한 다음, 맨 위에서 **+ 큐** 또는 **+ 토픽**을 선택하여 내보낼 큐 또는 토픽을 만듭니다.

내보내기 대상으로 Service Bus를 선택 하는 경우 큐 및 항목에는 세션이 나 중복 검색이 설정 되어 있지 않아야 합니다. 이러한 옵션 중 하나가 사용하도록 설정되면 일부 메시지가 큐 또는 토픽에 도착하지 않습니다.

### <a name="create-storage-account"></a>스토리지 계정 만들기

로 내보낼 기존 Azure Storage 계정이 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob Storage 계정](https://aka.ms/blobdocscreatestorageaccount) 만들기 또는 [v2 저장소 계정 Azure Data Lake Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md)에 대해 자세히 알아볼 수 있습니다.

    - Azure Data Lake Storage v2 저장소 계정으로 데이터를 내보내도록 선택 하는 경우 **계정 종류**로 **blobstorage** 를 선택 해야 합니다.
    - IoT Central 응용 프로그램에 대 한 것과 다른 구독에서 저장소 계정으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

이제 데이터를 내보낼 대상이 있으므로 다음 단계에 따라 연속 데이터 내보내기를 설정 합니다.

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽 창에서 **데이터 내보내기**를 선택 합니다.

    > [!Note]
    > 왼쪽 창에 데이터 내보내기가 표시 되지 않으면 앱에서 데이터 내보내기를 구성할 수 있는 권한이 없는 것입니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. 오른쪽 위에 있는 **+ 새로 만들기** 단추를 선택 합니다. **Azure Event Hubs**, **Azure Service Bus**또는 **azure Blob storage** 중 하나를 내보내기 대상으로 선택 합니다. 응용 프로그램당 최대 내보내기 수는 5 개입니다.

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data/export-new2.png)

4. 드롭다운 목록 상자에서 **Event Hubs 네임 스페이스**, **Service Bus 네임**스페이스, **저장소 계정 네임 스페이스**를 선택 하거나 **연결 문자열을 입력**합니다.

    - IoT Central 응용 프로그램과 동일한 구독에서 저장소 계정, Event Hubs 네임 스페이스 및 Service Bus 네임 스페이스도 볼 수 있습니다. 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.
    - 무료 가격 책정 계획을 사용 하 여 만든 앱의 경우 연속 데이터 내보내기를 구성 하는 유일한 방법은 연결 문자열을 사용 하는 것입니다. 무료 가격 책정 계획의 앱에는 연결 된 Azure 구독이 없습니다.

    ![새 이벤트 허브 만들기](media/howto-export-data/export-eh.png)

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

8. 연속 데이터 내보내기를 설정 하려면 **사용** 설정/해제가 **on**으로 설정 되어 있는지 확인 합니다. **저장**을 선택합니다.

9. 몇 분 후에 데이터가 선택한 대상에 표시 됩니다.

## <a name="export-contents-and-format"></a>콘텐츠 내보내기 및 형식

내보낸 원격 분석 데이터에는 원격 분석 값 자체 뿐만 아니라 장치 IoT Central 전송 된 전체 메시지가 포함 되어 있습니다. 내보낸 장치 데이터에는 모든 장치의 속성 및 메타 데이터에 대 한 변경 내용이 포함 되 고, 내보낸 장치 템플릿에는 모든 장치 템플릿에 대 한 변경 내용이 포함 됩니다.

Event Hubs 및 Service Bus의 경우 데이터를 거의 실시간으로 내보냅니다. 데이터는 본문 속성에 있고 JSON 형식입니다 (예제는 아래 참조).

Blob Storage의 경우 데이터는 분당 한 번 내보내집니다. 각 파일은 마지막으로 내보낸 파일 이후의 변경 내용 일괄 처리를 포함 합니다. 내보낸 데이터는 JSON 형식으로 3 개의 폴더에 배치 됩니다. 스토리지 계정의 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 장치 템플릿: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

파일로 이동 하 고 **Blob 편집** 탭을 선택 하 여 Azure Portal에서 내보낸 파일을 찾아볼 수 있습니다.


## <a name="telemetry"></a>원격 분석

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

## <a name="devices"></a>디바이스

스냅숏의 각 메시지 또는 레코드는 마지막으로 내보낸 메시지 이후 장치 및 해당 장치 및 클라우드 속성에 대 한 변경 내용을 하나 이상 나타냅니다. 다음 내용이 포함됩니다.

- IoT Central의 디바이스 `id`
- 디바이스의 `displayName`
- `instanceOf`의 장치 템플릿 Id
- `simulated` 플래그, 장치가 시뮬레이션 된 장치인 경우 true
- `provisioned` 플래그, 장치가 프로 비전 된 경우 true
- `approved` 플래그, 장치가 데이터를 보내도록 승인 된 경우 true
- 속성 값
- 장치 및 클라우드 속성 값을 포함 하 `properties`

삭제 된 장치는 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스에 대한 표시기가 없습니다.

Event Hubs 및 Service Bus의 경우 장치 데이터를 포함 하는 메시지는 IoT Central에 표시 되는 거의 실시간으로 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송 됩니다. 

Blob Storage의 경우 마지막으로 쓴 이후의 모든 변경 내용을 포함 하는 새 스냅숏은 분당 한 번 내보냅니다.

다음은 event hub 또는 Service Bus 큐 또는 토픽의 장치 및 속성 데이터에 대 한 예제 메시지입니다.

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
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
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Blob Storage의 장치 및 속성 데이터를 포함 하는 예제 스냅숏입니다. 내보낸 파일은 레코드 당 한 줄을 포함 합니다.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>디바이스 템플릿

각 메시지 또는 스냅숏 레코드는 마지막으로 내보낸 메시지 이후 게시 된 장치 템플릿에 대 한 변경 내용을 하나 이상 나타냅니다. 각 메시지 또는 레코드에서 전송 되는 정보는 다음과 같습니다.

- 위의 장치 스트림의 `instanceOf`와 일치 하는 장치 템플릿의 `id`
- 디바이스 템플릿의 `displayName`
- 장치 `capabilityModel` `interfaces`, 원격 분석, 속성 및 명령 정의를 포함 합니다.
- `cloudProperties` 정의
- 재정의 및 초기 값, `capabilityModel` 인라인

삭제 된 장치 템플릿은 내보내지지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스 템플릿에 대한 표시기가 없습니다.

Event Hubs 및 Service Bus의 경우 장치 템플릿 데이터가 포함 된 메시지는 IoT Central에 표시 되는 거의 실시간으로 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 전송 됩니다. 

Blob Storage의 경우 마지막으로 쓴 이후의 모든 변경 내용을 포함 하는 새 스냅숏은 분당 한 번 내보냅니다.

다음은 이벤트 허브의 장치 템플릿 데이터 또는 Service Bus 큐 또는 토픽에 대 한 예제 메시지입니다.

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
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
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>데이터 형식 변경 공지

> [!Note]
> 원격 분석 스트림 데이터 형식은 이러한 변경의 영향을 받지 않습니다. 데이터의 장치 및 장치 템플릿만 영향을 받습니다.

*장치* 및 *장치 템플릿* 스트림이 설정 된 상태에서 preview 응용 프로그램에 기존 데이터 내보내기가 있는 경우 내보내기를 **30 6 월 2020**로 업데이트 해야 합니다. 이는 Azure Blob Storage, Azure Event Hubs 및 Azure Service Bus에 대 한 내보내기에 적용 됩니다.

3 2020 월 3 일부 터 장치 및 장치 템플릿이 사용 하도록 설정 된 응용 프로그램의 모든 새 내보내기에는 위에서 설명한 데이터 형식이 포함 됩니다. 이전에 만든 모든 내보내기는 30 월 2020 일까 지 이전 데이터 형식으로 유지 됩니다. 그 후에는 이러한 내보내기가 자동으로 새 데이터 형식으로 마이그레이션됩니다. 새 데이터 형식은 IoT Central 공용 API의 장치, 장치 [속성](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [장치 클라우드 속성](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) 및 [장치 템플릿](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) 개체와 [일치 합니다.](https://docs.microsoft.com/rest/api/iotcentral/devices/get) 
 
**장치의**경우 이전 데이터 형식과 새 데이터 형식 간의 중요 한 차이점은 다음과 같습니다.
- 장치에 대 한 `@id` 제거 되며 `deviceId` 이름이로 바뀝니다 `id` 
- 장치의 프로 비전 상태를 설명 하는 `provisioned` 플래그가 추가 됩니다.
- 장치의 승인 상태를 설명 하는 `approved` 플래그가 추가 됩니다.
- 장치 및 클라우드 속성을 포함 하는 `properties` 공용 API의 엔터티와 일치

**장치 템플릿의**경우 이전 데이터 형식과 새 데이터 형식 간의 주목할 만한 차이점은 다음과 같습니다.

- 장치 템플릿의 `@id` 이름이 `id`
- 장치 템플릿에 대 한 `@type` 이름이 `types`로 바뀌고 이제 배열입니다.

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>장치 (지원 되지 않는 형식은 3 년 2 월 2020 일)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>장치 템플릿 (2020 년 2 월 3 일에 사용 되지 않는 형식)
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

이제 Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage으로 데이터를 내보내는 방법을 배웠으므로 다음 단계를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [Azure Functions 트리거 방법](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
