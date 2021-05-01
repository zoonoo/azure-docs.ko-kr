---
title: 이벤트 처리기 및 대상 - Azure Event Grid IoT Edge | Microsoft Docs
description: Edge의 Event Grid 이벤트 처리기 및 대상
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171587"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Edge의 Event Grid 이벤트 처리기 및 대상

이벤트 처리기는 추후 작업에 대한 이벤트나 이벤트를 처리하기 위한 위치입니다. 에지 모듈의 Event Grid 내에서 이벤트 처리기는 동일한 에지 디바이스, 다른 디바이스나 클라우드에 있을 수 있습니다. 이벤트를 처리하기 위해 다른 WebHook을 사용하거나 Azure Event Grid와 같은 네이티브 처리기 중 하나로 이벤트를 보낼 수 있습니다.

이 문서에서는 각각을 구성하는 방법에 대한 정보를 제공합니다.

## <a name="webhook"></a>WebHook

WebHook 엔드포인트에 게시하려면 `endpointType`을 `WebHook`으로 설정하고 다음을 입력합니다.

* endpointUrl: WebHook 엔드포인트 URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Event Grid 클라우드 엔드포인트에 게시하려면 `endpointType`을 `eventGrid`로 설정하고 다음을 입력합니다.

* endpointUrl: 클라우드의 Event Grid 토픽 URL
* sasKey: Event Grid 토픽의 SAS 키
* topicName: Event Grid로 나가는 이벤트에 스탬프하는 이름. 토픽 이름은 Event Grid 도메인 토픽에 게시할 때 유용합니다.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge 허브

에지 허브 모듈에 게시하려면 `endpointType`을 `edgeHub`로 설정하고 다음을 입력합니다.

* outputName: Event Grid 모듈이 구독을 edgeHub에 일치시키는 이벤트에 라우팅하는 출력입니다. 예를 들어 아래 구독과 일치하는 이벤트는/messages/modules/eventgridmodule/outputs/sampleSub4.에 기록됩니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Event Hubs에 게시하려면 `endpointType`을 `eventHub`로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Event Hubs 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Event Hubs로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성할 수 있습니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus 큐

Service Bus 큐를 게시하려면 `endpointType`을 `serviceBusQueue`로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Service Bus 큐 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Service Bus 큐로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus 토픽

Service Bus 토픽을 게시하려면 `endpointType`을 `serviceBusTopic`으로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Service Bus 토픽 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Service Bus 토픽으로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Storage 큐

스토리지 큐를 게시하려면 `endpointType`을 `storageQueue`로 설정하고 다음을 입력합니다.

* queueName: 게시하는 스토리지 큐의 이름입니다.
* connectionString: 스토리지 큐가 있는 스토리지 계정의 연결 문자열입니다.

    >[!NOTE]
    > Event Hubs, Service Bus 큐, Service Bus 토픽과 다르게 스토리지 큐에 사용된 연결 문자열은 엔터티와 관련되어 있지 않습니다. 대신 스토리지 계정에 대한 연결 문자열이어야 합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```