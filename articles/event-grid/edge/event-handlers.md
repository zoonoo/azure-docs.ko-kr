---
title: 이벤트 처리기 및 대상 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 에지의 이벤트 그리드에서 이벤트 처리기 및 대상
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849748"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>에지의 이벤트 그리드에서 이벤트 처리기 및 대상

이벤트 처리기는 추가 작업을 수행하거나 이벤트를 처리하기 위한 이벤트가 있는 위치입니다. 이벤트 그리드 온 에지 모듈을 사용하면 이벤트 처리기가 동일한 에지 장치, 다른 장치 또는 클라우드에 있을 수 있습니다. WebHook을 사용하여 이벤트를 처리하거나 Azure Event Grid와 같은 네이티브 처리기 중 하나에 이벤트를 보낼 수 있습니다.

이 문서에서는 각각을 구성하는 방법에 대한 정보를 제공합니다.

## <a name="webhook"></a>웹 후크

WebHook 끝점에 게시하려면 `endpointType` 다음을 `WebHook` 설정하고 다음을 제공합니다.

* 끝점Url: 웹후크 끝점 URL

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

Azure Event Grid 클라우드 끝점에 게시하려면 `endpointType` `eventGrid` 다음을 설정하고 다음을 제공합니다.

* endpointUrl: 클라우드의 이벤트 그리드 토픽 URL
* sasKey: 이벤트 그리드 토픽의 SAS 키
* topicName: 모든 발후 된 이벤트를 이벤트 그리드에 스탬프할 이름입니다. 주제 이름은 이벤트 그리드 도메인 토픽에 게시할 때 유용합니다.

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

## <a name="iot-edge-hub"></a>IoT 에지 허브

Edge Hub 모듈에 게시하려면 `endpointType` 다음을 `edgeHub` 설정하고 다음을 제공합니다.

* outputName: 이벤트 그리드 모듈이 이 구독과 일치하는 이벤트를 edgeHub로 라우팅하는 출력입니다. 예를 들어 아래 구독과 일치하는 이벤트는 /messages/module/eventgridmodule/출력/샘플Sub4에 기록됩니다.

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

이벤트 허브에 게시하려면 `endpointType` 다음을 `eventHub` 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 타겟팅하는 특정 이벤트 허브에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 이벤트 허브로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 연결 문자열을 생성할 수 있습니다.

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

서비스 버스 큐에 게시하려면 `endpointType` 다음을 `serviceBusQueue` 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 대상으로 하는 특정 서비스 버스 큐에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 서비스 버스 큐로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 connecection 문자열을 생성합니다.

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

서비스 버스 토픽에 게시하려면 `endpointType` `serviceBusTopic` 다음을 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 대상으로 하는 특정 서비스 버스 토픽에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 Service Bus 토픽으로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 connecection 문자열을 생성합니다.

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

저장소 큐에 게시하려면 `endpointType` 다음을 `storageQueue` 설정하고 다음을 제공합니다.

* queueName: 게시할 저장소 큐의 이름입니다.
* 연결문자열: 저장소 큐에 있는 저장소 계정에 대한 연결 문자열입니다.

    >[!NOTE]
    > Unline 이벤트 허브, 서비스 버스 큐 및 서비스 버스 항목, 저장소 큐에 사용되는 연결 문자열은 엔터티에 특정되지 않습니다. 대신 저장소 계정에 대한 연결 문자열을 사용해야 합니다.

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