---
title: 로컬이벤트 게시, 구독 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT Edge에서 이벤트 그리드를 사용하여 웹후크를 사용하여 로컬로 이벤트를 게시, 구독
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281003"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>튜토리얼: 로컬로 게시, 이벤트 구독

이 문서에서는 IoT Edge의 이벤트 그리드를 사용하여 이벤트를 게시하고 구독하는 데 필요한 모든 단계를 안내합니다.

> [!NOTE]
> Azure 이벤트 그리드 항목 및 구독에 대해 자세히 알아보려면 [이벤트 그리드 개념을](concepts.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free) 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치의](../../iot-edge/quickstart.md) 빠른 시작 단계를 따릅니다.

## <a name="deploy-event-grid-iot-edge-module"></a>이벤트 그리드 IoT 에지 모듈 배포

IoT Edge 장치에 모듈을 배포하는 방법에는 여러 가지가 있으며 모두 IoT Edge의 Azure 이벤트 그리드에서 작동합니다. 이 문서에서는 Azure 포털에서 IoT 가장자리에 이벤트 그리드를 배포하는 단계를 설명합니다.

>[!NOTE]
> 이 자습서에서는 지속성 없이 이벤트 그리드 모듈을 배포합니다. 즉, 모듈을 다시 배포할 때 이 자습서에서 만든 모든 토픽 및 구독이 삭제됩니다. 지속성 설정 방법에 대한 자세한 내용은 다음 문서를 참조하십시오: [Linux에서 상태를 유지하거나 Windows에서 상태를](persist-state-linux.md) [유지합니다.](persist-state-windows.md) 프로덕션 워크로드의 경우 지속성이 있는 Event Grid 모듈을 설치하는 것이 좋습니다.


### <a name="select-your-iot-edge-device"></a>IoT 에지 장치 선택

1. [Azure 포털에](https://portal.azure.com) 로그인
1. IoT Hub로 이동합니다.
1. **자동 장치 관리** 섹션의 메뉴에서 **IoT 가장자리를** 선택합니다. 
1. 장치 목록에서 대상 장치의 ID를 클릭합니다.
1. **모듈 설정**선택 . 페이지를 열어 두십시오. 다음 섹션의 단계를 계속합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure 포털에는 JSON 문서를 수동으로 빌드하는 대신 배포 매니페스트를 만드는 마법사가 있습니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. 배포 **모듈** 섹션에서 **추가**
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈을** 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: 이벤트 그리드 모듈
   * **이미지 URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
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
 1. **저장**을 클릭합니다.
 1. 다음 섹션으로 계속 하여 Azure 이벤트 그리드 구독자 모듈을 함께 배포하기 전에 추가합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 클라이언트 인증을 사용하지 않도록 설정한 이벤트 그리드 모듈을 배포합니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하도록 설정하는 것이 좋습니다. 이벤트 그리드 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오.
    > 
    > Azure VM을 에지 장치로 사용하는 경우 인바운드 포트 규칙을 추가하여 포트 4438에서 인바운드 트래픽을 허용합니다. 규칙 추가에 대한 지침은 [VM에 대한 포트를 여는 방법을](../../virtual-machines/windows/nsg-quickstart-portal.md)참조하십시오.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>이벤트 그리드 가입자 IoT 에지 모듈 배포

이 섹션에서는 이벤트를 전달할 수 있는 이벤트 처리기 역할을 하는 다른 IoT 모듈을 배포하는 방법을 보여 주며, 이 모듈을 배포하는 방법을 보여 주시면 됩니다.

### <a name="add-modules"></a>모듈 추가

1. 배포 모듈 섹션에서 다시 **추가를** **선택합니다.** 
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈을** 선택합니다.
1. 컨테이너의 이름, 이미지 및 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: 가입자
   * **이미지 URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **컨테이너 만들기 옵션**: 없음
1. **저장**을 클릭합니다.
1. **다음을** 클릭하여 경로 섹션을 계속합니다.

 ### <a name="setup-routes"></a>설정 경로

기본 경로를 유지하고 **다음을** 선택하여 검토 섹션으로 계속

### <a name="submit-the-deployment-request"></a>배포 요청 제출

1. 검토 섹션에는 이전 섹션의 선택에 따라 만들어진 JSON 배포 매니페스트가 표시됩니다. JSON에 나열된 **eventgridmodule** 및 **구독자의** 모듈이 모두 표시되는지 확인합니다. 
1. 배포 정보를 검토한 다음 **제출**을 선택합니다. 배포를 제출한 후 **장치** 페이지로 돌아갑니다.
1. 모듈 **섹션에서** **eventgrid** 및 **구독자** 모듈이 모두 나열되어 있는지 확인합니다. 또한 **배포에서 지정및** **장치 열에 의해 보고됨이** **예로**설정되어 있는지 확인합니다.

    모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="create-a-topic"></a>토픽 만들기

이벤트 게시자는 이벤트 그리드 항목을 만들어야 합니다. Azure 이벤트 그리드에서 토픽은 게시자가 이벤트를 보낼 수 있는 끝점을 참조합니다.

1. 다음 콘텐츠로 topic.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md)참조하십시오.

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 다음 명령을 실행하여 이벤트 그리드 항목을 만듭니다. HTTP 상태 코드가 있는지 `200 OK`확인합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행하여 항목이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

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

구독자는 토픽에 게시된 이벤트에 등록할 수 있습니다. 이벤트를 받으려면 관심 있는 주제에 대한 이벤트 그리드 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠로 subscription.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

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
    > **endpointType** 속성은 구독자가 **Webhook입니다.**  **endpointUrl은** 구독자가 이벤트를 수신 대기하는 URL을 지정합니다. 이 URL은 이전에 배포한 Azure 구독자 샘플에 해당합니다.
2. 다음 명령을 실행하여 토픽에 대한 구독을 만듭니다. HTTP 상태 코드가 있는지 `200 OK`확인합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

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

1. 다음 콘텐츠로 event.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md)참조하십시오.

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

## <a name="verify-event-delivery"></a>이벤트 전달 확인

1. IoT 에지 VM에 SSH 또는 RDP를 제공합니다.
1. 구독자 로그를 확인합니다.

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

* 다음 명령을 실행하여 토픽 및 모든 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* IoT Edge 장치에서 가입자 모듈을 삭제합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 이벤트 그리드 항목, 구독 및 게시된 이벤트를 만들었습니다. 이제 기본 단계를 알고 있으므로 다음 문서를 참조하십시오. 

- IoT Edge에서 Azure 이벤트 그리드를 사용하는 문제를 해결하려면 [문제 해결 가이드를](troubleshoot.md)참조하십시오.
- [필터를](advanced-filtering.md)사용하여 구독을 생성/업데이트합니다.
- [리눅스](persist-state-linux.md) 또는 [Windows에서](persist-state-windows.md) 이벤트 그리드 모듈의 지속성 사용
- [설명서를](configure-client-auth.md) 따라 클라이언트 인증 구성
- 이 [자습서를](pub-sub-events-webhook-cloud.md) 따라 이벤트를 클라우드의 Azure 함수로 전달
- [IoT 에지의 Blob 스토리지 이벤트에 대응](react-blob-storage-events-locally.md)
- [에지에서 주제 및 구독 모니터링](monitor-topics-subscriptions.md)

