---
title: 이벤트 처리기 및 대상-Azure Event Grid IoT Edge | Microsoft Docs
description: Edge의 Event Grid에 있는 이벤트 처리기 및 대상
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171587"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Edge의 Event Grid에 있는 이벤트 처리기 및 대상

이벤트 처리기는 추가 작업에 대 한 이벤트가 발생 하거나 이벤트를 처리 하는 위치입니다. Edge 모듈의 Event Grid을 사용 하 여 이벤트 처리기는 동일한 Edge 장치, 다른 장치 또는 클라우드에 있을 수 있습니다. 모든 WebHook를 사용 하 여 이벤트를 처리 하거나 Azure Event Grid 같은 기본 처리기 중 하나로 이벤트를 보낼 수 있습니다.

이 문서에서는 각를 구성 하는 방법에 대 한 정보를 제공 합니다.

## <a name="webhook"></a>WebHook

WebHook 끝점에 게시 하려면를로 설정 하 `endpointType` `WebHook` 고 다음을 제공 합니다.

* endpointUrl: WebHook 끝점 URL

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

Azure Event Grid 클라우드 끝점에 게시 하려면를로 설정 하 `endpointType` `eventGrid` 고 다음을 제공 합니다.

* endpointUrl: 클라우드의 Event Grid 토픽 URL
* sasKey: Event Grid 토픽의 SAS 키
* topicName: Event Grid로 나가는 모든 이벤트를 스탬프 하는 이름입니다. 항목 이름은 Event Grid 도메인 항목에 게시 하는 경우에 유용 합니다.

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

Edge 허브 모듈에 게시 하려면를로 설정 하 `endpointType` `edgeHub` 고 다음을 제공 합니다.

* outputName: Event Grid 모듈이이 구독과 일치 하는 이벤트를 edgeHub로 라우팅하는 출력입니다. 예를 들어 아래 구독과 일치 하는 이벤트는/messages/modules/eventgridmodule/outputs/sampleSub4.에 기록 됩니다.

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

이벤트 허브에 게시 하려면를로 설정 하 `endpointType` `eventHub` 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 대상으로 생성 되는 특정 이벤트 허브에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 이벤트 허브로 이동 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 하 여 엔터티 관련 연결 문자열을 생성할 수 있습니다.

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

Service Bus 큐에 게시 하려면를로 설정 하 `endpointType` `serviceBusQueue` 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 대상으로 생성 되는 특정 Service Bus 큐에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 Service Bus 큐로 이동 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 하 여 엔터티 관련 연결 문자열을 생성 합니다.

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

Service Bus 항목에 게시 하려면를로 설정 하 `endpointType` `serviceBusTopic` 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 생성 되는 대상으로 지정 된 특정 Service Bus 항목에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 Service Bus 토픽으로 이동 하 여 엔터티 관련 연결 문자열을 생성 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 합니다.

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

저장소 큐에 게시 하려면를로 설정 하  `endpointType` `storageQueue` 고 다음을 제공 합니다.

* queueName: 게시 하는 저장소 큐의 이름입니다.
* connectionString: 저장소 큐가 있는 저장소 계정에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 줄 Event Hubs, Service Bus 큐 및 Service Bus 항목은 저장소 큐에 사용 되는 연결 문자열은 엔터티만 관련 되지 않습니다. 대신 저장소 계정에 대 한 연결 문자열 이어야 합니다.

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