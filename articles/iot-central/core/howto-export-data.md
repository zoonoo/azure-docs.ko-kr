---
title: Azure IoT 중앙 데이터 내보내기 | 마이크로 소프트 문서
description: Azure IoT Central 응용 프로그램에서 Azure 이벤트 허브, Azure 서비스 버스 및 Azure Blob 저장소로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157911"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Azure의 대상으로 IoT 데이터 내보내기

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 Azure IoT Central의 연속 데이터 내보내기 기능을 사용하여 데이터를 Azure 이벤트 허브, **Azure Service Bus**또는 Azure **Blob 저장소** 인스턴스로 내보내는 방법을 **설명합니다.** 데이터는 JSON 형식으로 내보내며 원격 분석, 장치 정보 및 장치 템플릿 정보를 포함할 수 있습니다. 내보낸 데이터를 다음의 용도로 사용합니다.

- 웜 경로 인사이트 및 분석. 이 옵션에는 Azure Stream Analytics에서 사용자 지정 규칙을 트리거하거나, Azure 논리 앱에서 사용자 지정 워크플로를 트리거하거나, 변환할 Azure Functions를 통해 전달합니다.
- Azure 기계 학습의 학습 모델 또는 Microsoft Power BI의 장기 추세 분석과 같은 콜드 경로 분석입니다.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.

## <a name="prerequisites"></a>사전 요구 사항

IoT Central 응용 프로그램의 관리자이거나 데이터 내보내기 권한이 있어야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

연속 데이터 내보내기를 구성하기 전에 내보내기 대상이 있어야 합니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

내보낼 기존 이벤트 허브 네임스페이스가 없는 경우 다음 단계를 따르세요.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다. IoT Central 응용 프로그램과 동일한 구독에 없는 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

3. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브**를 선택하여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

내보낼 기존 Service Bus 네임스페이스가 없는 경우 다음 단계를 따르십시오.

1. Azure [포털에서 새 서비스 버스 네임스페이스를](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.
2. 구독을 선택합니다. IoT Central 응용 프로그램과 동일한 구독에 없는 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

3. Service Bus 네임스페이스로 이동한 다음, 맨 위에서 **+ 큐** 또는 **+ 토픽**을 선택하여 내보낼 큐 또는 토픽을 만듭니다.

서비스 버스를 내보내기 대상으로 선택하면 큐 및 토픽에 세션 또는 중복 검색이 활성화되어 있지 않아야 합니다. 이러한 옵션 중 하나가 사용하도록 설정되면 일부 메시지가 큐 또는 토픽에 도착하지 않습니다.

### <a name="create-storage-account"></a>스토리지 계정 만들기

내보낼 기존 Azure Storage 계정이 없는 경우 다음 단계를 따르십시오.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob 저장소 계정](https://aka.ms/blobdocscreatestorageaccount) 또는 [Azure Data Lake Storage v2 저장소 계정을](../../storage/blobs/data-lake-storage-quickstart-create-account.md)만드는 방법에 대해 자세히 알아볼 수 있습니다. 데이터 내보내기는 블록 Blob을 지원하는 저장소 계정에만 데이터를 쓸 수 있습니다. 다음은 알려진 호환 가능한 저장소 계정 유형의 목록입니다. 

    |성능 계층|계정 유형|
    |-|-|
    |Standard|범용 V2|
    |Standard|범용 V1|
    |Standard|Blob Storage|
    |Premium|블록 Blob 스토리지|

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

이제 데이터를 내보낼 대상이 되었으므로 다음 단계에 따라 연속 데이터 내보내기를 설정합니다.

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽 창에서 데이터 **내보내기를**선택합니다.

    > [!Note]
    > 왼쪽 창에 데이터 내보내기가 표시되지 않으면 앱에서 데이터 내보내기를 구성할 수 있는 권한이 없습니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. 오른쪽 상단에 있는 **+ 새** 버튼을 선택합니다. **Azure 이벤트 허브,** **Azure 서비스 버스**또는 **Azure Blob 저장소** 중 하나를 내보내기의 대상으로 선택합니다. 응용 프로그램당 최대 내보내기 수는 5개입니다.

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data/new-export-definition.png)

4. 드롭다운 목록 상자에서 이벤트 **허브 네임스페이스,** **서비스 버스 네임스페이스,** **저장소 계정 네임스페이스를**선택하거나 **연결 문자열을 입력합니다.**

    - IoT Central 응용 프로그램과 동일한 구독에 저장소 계정, 이벤트 허브 네임스페이스 및 서비스 버스 네임스페이스만 표시됩니다. 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.
    - 무료 가격 책정 계획을 사용하여 만든 앱의 경우 연속 데이터 내보내기를 구성하는 유일한 방법은 연결 문자열을 사용하는 것입니다. 무료 요금제의 앱에는 연결된 Azure 구독이 없습니다.

    ![새 이벤트 허브 만들기](media/howto-export-data/export-event-hub.png)

5. (선택 사항) **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 다음 항목의 연결 문자열을 가져오려면
    - 이벤트 허브 또는 서비스 버스는 Azure 포털의 네임스페이스로 이동합니다.
        - **설정에서** **공유 액세스 정책을** 선택합니다.
        - 기본 **RootManageSharedAccessKey**를 선택하거나 새로 만듭니다.
        - 주 또는 보조 연결 문자열 중 하나를 복사합니다.
    - 저장소 계정은 Azure 포털의 저장소 계정으로 이동합니다.
        - **설정에서** **액세스 키를 선택합니다.**
        - key1 연결 문자열 또는 key2 연결 문자열 복사

6. 드롭다운 목록 상자에서 이벤트 허브, 큐, 토픽 또는 컨테이너를 선택합니다.

7. **내보낼 데이터에서**형식을 **On으로**설정하여 내보낼 데이터 유형을 선택합니다.

8. 연속 데이터 내보내기를 사용 설정하려면 **사용 가능한** 토글이 **켜졌는지**확인합니다. **저장**을 선택합니다.

9. 몇 분 후 선택한 대상에 데이터가 표시됩니다.

## <a name="export-contents-and-format"></a>콘텐츠 및 형식 내보내기

내보낸 원격 분석 데이터에는 원격 분석 값 자체뿐만 아니라 IoT Central으로 전송된 메시지 전체가 포함됩니다. 내보낸 장치 데이터에는 모든 장치의 속성 및 메타데이터에 대한 변경 내용이 포함되며 내보낸 장치 템플릿에는 모든 장치 템플릿에 대한 변경 내용이 포함되어 있습니다.

이벤트 허브 및 서비스 버스의 경우 데이터는 거의 실시간으로 내보내됩니다. 데이터는 본문 속성에 있으며 JSON 형식입니다(예: 아래 참조).

Blob Storage의 경우 데이터는 분당 한 번 내보내며 각 파일에는 마지막으로 내보낸 파일 이후의 변경 일괄 처리가 포함됩니다. 내보낸 데이터는 JSON 형식으로 세 개의 폴더에 배치됩니다. 스토리지 계정의 기본 경로는 다음과 같습니다.

- 원격 분석: _{컨테이너}/{앱 id}/원격 분석/{YYY}/{MM}/{mm}/{hh}/{mm}/{파일 이름}_
- 장치: _{컨테이너}/{앱 id}/{앱 id}/장치/{YYY}/{MM}/{mm}/{hh}/{mm}/{파일 이름}_
- 장치 템플릿: _{컨테이너}/{앱 id}/장치 템플릿/{YYY}/{MM}/{mm}/{hh}/{mm}/{파일 이름}_

Azure 포털에서 내보낸 파일을 파일로 이동하여 **Blob 편집** 탭을 선택하여 탐색할 수 있습니다.


## <a name="telemetry"></a>원격 분석

이벤트 허브 및 서비스 버스의 경우 IoT Central이 장치에서 메시지를 수신한 후 새 메시지가 빠르게 내보내지며, 내보낸 각 메시지에는 장치가 본문 속성에 보낸 전체 메시지가 JSON 형식으로 포함되어 있습니다.

Blob Storage의 경우 메시지는 분당 한 번 일괄 처리되고 내보내됩니다. 내보낸 파일은 [IoT Hub 메시지 라우팅에서](../../iot-hub/tutorial-routing.md) Blob 저장소로 내보낸 메시지 파일과 동일한 형식을 사용합니다. 

> [!NOTE]
> `contentType: application/JSON` Blob Storage의 경우 장치가 있는 메시지(또는 `contentEncoding:utf-8` `utf-16`)를 `utf-32`보내고 있는지 확인합니다. 예제는 [IoT Hub 설명서를](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) 참조하십시오.

원격 분석을 보낸 장치는 장치 ID로 표시됩니다(다음 섹션 참조). 장치 이름을 얻으려면 장치 데이터를 내보내고 장치 메시지의 **장치Id와** 일치하는 **연결DeviceId를** 사용하여 각 메시지를 상호 연결합니다.

이벤트 허브 또는 서비스 버스 큐 또는 토픽에서 받은 예제 메시지입니다.

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

다음은 Blob 저장소로 내보내는 예제 레코드입니다.

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

스냅숏의 각 메시지 또는 레코드는 마지막으로 내보낸 메시지 이후 장치 및 해당 장치 및 클라우드 속성에 대한 하나 이상의 변경 내용을 나타냅니다. 다음 내용이 포함됩니다.

- IoT Central의 디바이스 `id`
- 디바이스의 `displayName`
- 장치 템플릿 ID 에서`instanceOf`
- `simulated`장치가 시뮬레이션된 장치인 경우 플래그, true
- `provisioned`플래그, 장치가 프로비전된 경우 true
- `approved`플래그, 장치가 데이터 전송을 승인한 경우 true
- 속성 값
- `properties`장치 및 클라우드 속성 값 포함

삭제된 장치는 내보내지지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스에 대한 표시기가 없습니다.

이벤트 허브 및 서비스 버스의 경우 장치 데이터가 포함된 메시지는 IoT Central에 나타나는 대로 거의 실시간으로 이벤트 허브 또는 서비스 버스 큐 또는 토픽으로 전송됩니다. 

Blob Storage의 경우 마지막으로 작성된 스냅숏이 분당 한 번 내보내진 이후의 모든 변경 내용을 포함하는 새 스냅숏입니다.

이벤트 허브 또는 Service Bus 큐 또는 토픽의 장치 및 속성 데이터에 대한 예제 메시지입니다.

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

Blob Storage의 장치 및 속성 데이터가 포함된 예제 스냅숏입니다. 내보낸 파일에는 레코드당 한 줄이 포함됩니다.

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

각 메시지 또는 스냅숏 레코드는 마지막으로 내보낸 메시지 이후 게시된 장치 템플릿에 대한 하나 이상의 변경 내용을 나타냅니다. 각 메시지 또는 레코드에서 보낸 정보는 다음과 같습니다.

- `id`위의 장치 스트림과 `instanceOf` 일치하는 장치 템플릿의
- 디바이스 템플릿의 `displayName`
- 의 및 원격 분석, 속성 및 명령 정의를 포함하는 장치 `capabilityModel` `interfaces`
- `cloudProperties`정의
- 재정의 및 초기 값과 인라인`capabilityModel`

삭제된 장치 템플릿은 내보내지지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스 템플릿에 대한 표시기가 없습니다.

이벤트 허브 및 서비스 버스의 경우 장치 템플릿 데이터가 포함된 메시지는 IoT Central에 나타나는 대로 거의 실시간으로 이벤트 허브 또는 서비스 버스 큐 또는 토픽으로 전송됩니다. 

Blob Storage의 경우 마지막으로 작성된 스냅숏이 분당 한 번 내보내진 이후의 모든 변경 내용을 포함하는 새 스냅숏입니다.

이벤트 허브 또는 Service Bus 큐 또는 토픽의 장치 템플릿 데이터에 대한 예제 메시지입니다.

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

Blob Storage의 장치 및 속성 데이터가 포함된 예제 스냅숏입니다. 내보낸 파일에는 레코드당 한 줄이 포함됩니다.

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
> 원격 분석 스트림 데이터 형식은 이 변경의 영향을 받지 않습니다. 데이터 스트림의 장치 및 장치 템플릿만 영향을 받습니다.

*장치* 및 *장치 템플릿* 스트림이 켜져 있는 미리 보기 응용 프로그램에서 기존 데이터 내보내기가 켜져 있는 경우 **2020년 6월 30일까지**내보내기를 업데이트해야 합니다. 이는 Azure Blob 저장소, Azure 이벤트 허브 및 Azure 서비스 버스에 대한 내보내기에 적용됩니다.

2020년 2월 3일부터 장치 및 장치 템플릿을 사용하도록 설정한 응용 프로그램의 모든 새 내보내기에는 위에서 설명한 데이터 형식이 갖습니다. 이전에 생성된 모든 내보내기는 2020년 6월 30일까지 이전 데이터 형식에 유지되며, 이 후에는 이러한 내보내기가 새 데이터 형식으로 자동으로 마이그레이션됩니다. 새 데이터 형식은 IoT Central 공용 API의 [장치,](https://docs.microsoft.com/rest/api/iotcentral/devices/get) [장치 속성,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [장치 클라우드 속성](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) 및 장치 [템플릿](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) 개체와 일치합니다. 
 
**장치의**경우 이전 데이터 형식과 새 데이터 형식 간의 주목할 만한 차이점은 다음과 같습니다.
- `@id`장치가 제거되면 `deviceId` 이름이 바뀝니다.`id` 
- `provisioned`장치의 프로비저닝 상태를 설명하기 위해 플래그가 추가됩니다.
- `approved`장치의 승인 상태를 설명하기 위해 플래그가 추가됩니다.
- `properties`장치 및 클라우드 속성을 포함하여 공용 API의 엔터티일치

**장치 템플릿의**경우 이전 데이터 형식과 새 데이터 형식 간의 주목할 만한 차이점은 다음과 같습니다.

- `@id`장치 템플릿의 이름이 바뀌는 경우`id`
- `@type`장치 템플릿의 이름이 `types`로 바뀌고 이제 배열이 됩니다.

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>장치(2020년 2월 3일 현재 더 이상 사용되지 않는 형식)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>장치 템플릿(2020년 2월 3일 현재 더 이상 사용되지 않습니다.
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

Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage로 데이터를 내보내는 방법을 알고 있으므로 다음 단계로 계속 진행합니다.

> [!div class="nextstepaction"]
> [웹후크 를 만드는 방법](./howto-create-webhooks.md)
