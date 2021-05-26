---
title: 로컬로 이벤트 게시, 구독 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge의 Event Grid와 웹후크를 사용하여 로컬로 이벤트를 게시하고 구독합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 4080fc274f03cbc4e5ba48b3c3e2ac5699886c96
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377212"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>자습서: 로컬로 이벤트 게시, 구독

이 문서에서는 IoT Edge의 Event Grid를 사용하여 이벤트를 게시하고 구독하는 데 필요한 모든 단계를 설명합니다.

> [!NOTE]
> Azure Event Grid 토픽 및 구독에 대한 자세한 내용은 [Event Grid 개념](concepts.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 디바이스** - 디바이스가 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 디바이스](../../iot-edge/quickstart.md)용 빠른 시작의 단계를 따릅니다.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 디바이스에 모듈을 배포하는 방법은 여러 가지이며 해당 방법은 모두 IoT Edge의 Azure Event Grid에서 작동합니다. 이 문서에서는 Azure Portal에서 IoT Edge의 Event Grid를 배포하는 단계를 설명합니다.

>[!NOTE]
> 이 자습서에서는 지속성을 사용하지 않는 Event Grid 모듈을 배포합니다. 즉, 모듈을 다시 배포하는 경우 이 자습서에서 만든 모든 토픽 및 구독은 삭제됩니다. 지속성을 설정하는 방법에 대한 자세한 내용은 [Linux에서 상태 유지](persist-state-linux.md) 또는 [Windows에서 상태 유지](persist-state-windows.md) 문서를 참조하세요. 프로덕션 워크로드의 경우 지속성을 사용하는 Event Grid 모듈을 설치하는 것이 좋습니다.


### <a name="select-your-iot-edge-device"></a>IoT Edge 디바이스 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 디바이스 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택합니다. 
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정** 을 선택합니다. 이 페이지를 열린 상태로 유지합니다. 다음 섹션에서 단계를 계속 진행합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에서는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사를 제공합니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택합니다.
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: eventgridmodule
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. 페이지 맨 아래에 있는 **저장**
 1. 다음 섹션을 계속 진행하여 Azure Event Grid 구독자 모듈을 추가한 후 함께 배포합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 클라이언트 인증을 사용하지 않도록 설정하여 Event Grid 모듈을 배포합니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하도록 설정하는 것이 좋습니다. Event Grid 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조하세요.
    > 
    > Azure VM을 에지 디바이스로 사용하는 경우 포트 4438에서 인바운드 트래픽을 허용하는 인바운드 포트 규칙을 추가합니다. 규칙을 추가하는 방법에 대한 지침은 [VM에 포트를 여는 방법](../../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid 구독자 IoT Edge 모듈 배포

이 섹션에서는 이벤트를 전송할 수 있는 이벤트 처리기 역할을 하는 다른 IoT 모듈을 배포하는 방법을 보여 줍니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 다시 선택합니다. 
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: 구독자
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **컨테이너 만들기 옵션**: 없음
1. 페이지 맨 아래에 있는 **저장**
1. **다음** 을 클릭하여 경로 섹션에서 계속합니다.

 ### <a name="setup-routes"></a>경로 설정

기본 경로를 유지하고 **다음** 을 선택하여 검토 섹션에서 계속합니다.

### <a name="submit-the-deployment-request"></a>배포 요청 제출

1. 검토 섹션에서는 이전 섹션의 선택 사항에 따라 만들어진 JSON 배포 매니페스트를 보여 줍니다. **eventgridmodule** 및 **subscriber** 모듈이 둘 다 JSON에 나열되는지 확인합니다. 
1. 배포 정보를 검토한 다음 **제출** 을 선택합니다. 배포를 제출한 후 **디바이스** 페이지로 돌아갑니다.
1. **모듈 섹션** 에서 **eventgrid** 및 **subscriber** 모듈이 둘 다 나열되는지 확인합니다. **배포에 지정됨** 및 **디바이스에서 보고됨** 열이 **예** 로 설정되었는지도 확인합니다.

    모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="create-a-topic"></a>토픽 만들기

이벤트 게시자는 Event Grid 토픽을 만들어야 합니다. Azure Event Grid에서 토픽은 게시자가 이벤트를 송신할 수 있는 엔드포인트를 나타냅니다.

1. 다음 콘텐츠를 사용하여 topic.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)을 참조하세요.

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 다음 명령을 실행하여 Event Grid 토픽을 만듭니다. HTTP 상태 코드가 `200 OK`인지 확인합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행하여 토픽이 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시된 이벤트를 등록할 수 있습니다. 이벤트를 수신하려면 관심 있는 토픽에 대한 Event Grid 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠를 사용하여 subscription.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **endpointType** 속성은 구독자가 **웹후크** 임을 지정합니다.  **endpointUrl** 은 구독자가 이벤트를 수신 대기하는 URL을 지정합니다. 이 URL은 앞에서 배포한 Azure 구독자 샘플에 해당합니다.
2. 다음 명령을 실행하여 토픽에 대해 구독을 만듭니다. HTTP 상태 코드가 `200 OK`인지 확인합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행하여 구독이 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠로 event.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)을 참조하세요.

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. 다음 명령을 실행하여 이벤트를 게시합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 전송 확인

1. SSH 또는 RDP를 수행하여 IoT Edge VM에 연결합니다.
1. 다음과 같이 구독자 로그를 확인합니다.

    Windows에서 다음 명령을 실행합니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Linux에서는 다음 명령을 실행합니다.

    ```sh
    sudo docker logs subscriber
    ```

    샘플 출력:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 토픽 및 모든 해당 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* IoT Edge 디바이스에서 구독자 모듈을 삭제합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Event Grid 토픽, 구독 및 게시된 이벤트를 만들었습니다. 기본 단계를 배웠으므로 이제 다음 문서를 참조하세요. 

- IoT Edge에서 Azure Event Grid 사용과 관련된 문제를 해결하려면 [문제 해결 가이드](troubleshoot.md)를 참조하세요.
- [필터](advanced-filtering.md)를 사용하여 구독을 만들거나 업데이트합니다.
- [Linux](persist-state-linux.md) 또는 [Windows](persist-state-windows.md)에서 Event Grid 모듈의 지속성을 사용합니다.
- 클라이언트 인증을 구성하려면 [설명서](configure-client-auth.md)에 따르세요.
- 이 [자습서](pub-sub-events-webhook-cloud.md)에 따라 클라우드의 Azure Functions에 이벤트를 전달합니다.
- [IoT Edge에서 Blob Storage 이벤트에 대응](react-blob-storage-events-locally.md)합니다.
- [Edge에서 토픽 및 구독을 모니터링](monitor-topics-subscriptions.md)합니다.

