---
title: Kubernetes의 Azure Event Grid - 이벤트 처리기 및 대상
description: 이 문서에서는 Kubernetes의 Event Grid에서 지원하는 다양한 유형의 이벤트 처리기 및 대상을 설명합니다.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 838084204ed2c1979f618bb2bfe644d1f88cd51e
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536502"
---
# <a name="event-handlers-destinations-in-event-grid-on-kubernetes"></a>Kubernetes의 Event Grid의 이벤트 처리기 대상
이벤트 처리기는 엔드포인트를 노출하는 시스템으로, Event Grid에서 이벤트를 보내는 대상입니다. 이벤트를 수신하는 이벤트 처리기는 이에 따라 작동하고 이벤트 페이로드를 사용하여 일부 논리를 실행하므로 새 이벤트가 발생할 수 있습니다.

이벤트를 대상으로 전송하도록 Event Grid를 구성하려면 이벤트 구독을 만듭니다. [Azure CLI](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create), [관리 SDK](../sdk-overview.md#management-sdks) 또는 [2020-10-15-preview API](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate) 버전을 사용하는 직접 HTTPS 호출을 통해 이 작업을 수행할 수 있습니다.

일반적으로 Kubernetes의 Event Grid는 **웹후크** 를 통해 모든 대상에 이벤트를 보낼 수 있습니다. 웹후크는 Event Grid가 액세스할 수 있는 서비스 또는 워크로드에서 노출하는 HTTP(s) 엔드포인트입니다. 웹후크는 클라우드, 온-프레미스 또는 Event Grid가 도달할 수 있는 모든 위치의 동일한 클러스터, 동일한 네트워크 공간에서 호스트되는 워크로드일 수 있습니다. 

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

Event Grid는 웹후크를 통해 **Kubernetes 클러스터에서 호스트되는** 다음 대상을 지원합니다.

* Azure Arc를 사용하는 Kubernetes의 Azure App Service. 
* Azure Arc를 사용하는 Kubernetes의 Azure Functions. 
* Azure Arc를 사용하는 Kubernetes의 Azure Logic Apps.

Kubernetes의 Event Grid는 웹후크 외에 **Azure에서 호스트되는** 다음 대상에 이벤트를 보낼 수 있습니다.

- **웹후크를 사용하는** Azure Event Grid
- **웹후크만 사용하는** Azure Functions
- Azure Resource Manager 리소스 ID를 사용하는 Azure Event Hubs
- Azure Resource Manager 리소스 ID를 사용하는 Azure Service Bus 토픽 또는 큐
- Azure Resource Manager 리소스 ID를 사용하는 Azure Storage 큐



## <a name="feature-parity"></a>기능 패리티
Kubernetes의 Event Grid는 이벤트 구독에 대한 Azure Event Grid 지원과 함께 훌륭한 수준의 기능 패리티를 제공합니다. 다음 목록에서는 이벤트 구독 기능의 주요 차이점을 열거합니다. 이러한 차이점 외에도 Kubernetes의 Event Grid에서 이벤트 구독을 관리할 때 Azure Event Grid의 [REST API 버전 2020-10-15-preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions)를 참조로 사용할 수 있습니다.

1. [REST API 버전 2020-10-15-preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions)를 사용합니다.
2. [Azure Functions용 Azure Event Grid 트리거](../../azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp%2Cconsole)는 지원되지 않습니다. WebHook 대상 유형을 사용하여 Azure Functions에 이벤트를 전달할 수 있습니다.
3. [배달 못한 편지 위치](../manage-event-delivery.md#set-dead-letter-location) 지원은 없습니다. 즉, 이벤트 구독 페이로드에서 ``properties.deadLetterDestination``을 사용할 수 없습니다.
4. Azure Relay의 하이브리드 연결은 아직 지원되지 않습니다.
5. CloudEvents 스키마만 지원됩니다. 지원되는 스키마 값은 "[CloudEventSchemaV1_0](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#eventdeliveryschema)"입니다. 클라우드 이벤트 스키마는 확장 가능하며 개방형 표준을 기반으로 합니다.  
6. 레이블([properties.labels](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#request-body))은 Kubernetes의 Event Grid에 적용할 수 없습니다. 따라서 사용할 수 없습니다.
7. [리소스 ID를 사용한 배달](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#deliverywithresourceidentity)은 지원되지 않습니다. 따라서 [이벤트 구독 ID](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#eventsubscriptionidentity)에 대한 모든 속성이 지원되지 않습니다.
8. [대상 엔드포인트 유효성 검사](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events)는 아직 지원되지 않습니다.

## <a name="event-filtering-in-event-subscriptions"></a>이벤트 구독의 이벤트 필터링
이벤트 구독을 구성할 때 또 다른 중요한 부분은 대상에 배달해야 할 이벤트를 선택하는 것입니다. 자세한 내용은 [이벤트 필터링](filter-events.md)을 참조하세요.

## <a name="sample-destination-configurations"></a>샘플 대상 구성

다음은 원하는 대상에 따른 몇 가지 기본적인 샘플 구성입니다.

## <a name="webhook"></a>WebHook
WebHook 엔드포인트에 게시하려면 `endpointType`을 `WebHook`으로 설정하고 다음을 입력합니다.

* **endpointUrl**: 웹후크 엔드포인트 URL

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

Azure Event Grid 클라우드 엔드포인트에 게시하려면 `endpointType`을 `WebHook`로 설정하고 다음을 입력합니다.

* **endpointUrl**: API 버전 매개 변수가 **2018-01-01** 로 설정되고 `aeg-sas-key`가 URL 인코딩 SAS 키로 설정된 클라우드의 Azure Event Grid 토픽 URL입니다. 

   ```json
    {
        "properties": {
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01&aeg-sas-key=urlencoded(sas-key-value)"
                }
            }
        }
    }
   ```

## <a name="event-hubs"></a>Event Hubs

Event Hubs에 게시하려면 `endpointType`을 `eventHub`로 설정하고 다음을 입력합니다.

* **resourceId**: 특정 이벤트 허브의 리소스 ID

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "resourceId": "<Azure Resource ID of your event hub>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus 큐

Service Bus 큐에 게시하려면 `endpointType`을 `serviceBusQueue`로 설정하고 다음을 입력합니다.

* **resourceId**: 특정 Service Bus 큐의 리소스 ID

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus queue>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus 토픽

Service Bus 토픽을 게시하려면 `endpointType`을 `serviceBusTopic`으로 설정하고 다음을 입력합니다.

* **resourceId**: 특정 Service Bus 토픽의 리소스 ID

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus topic>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Storage 큐

스토리지 큐를 게시하려면 `endpointType`을 `storageQueue`로 설정하고 다음을 입력합니다.

* **queueName**: 게시할 Azure Storage의 이름
* **resourceID**: 큐가 포함된 스토리지 계정의 Azure 리소스 ID

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "resourceId": "<Azure Resource ID of your Storage account>"
              }
            }
          }
        }
    ```

## <a name="next-steps"></a>다음 단계
* 이벤트 구독에 [필터 구성](filter-events.md)을 추가하여 배달할 이벤트를 선택합니다. 
* Kubernetes용 Azure Arc의 Event Grid에서 지원하는 스키마에 대해 알아보려면 [Kubernetes의 Event Grid - 이벤트스키마](event-schemas.md)를 참조하세요.