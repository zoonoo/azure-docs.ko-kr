---
title: Blob Storage module 이벤트에 대응-Azure Event Grid IoT Edge | Microsoft Docs
description: Blob Storage module 이벤트에 대응
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77086598"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>자습서: IoT Edge에서 Blob Storage 이벤트에 대응 (미리 보기)
이 문서에서는 IoT 모듈에 Azure Blob Storage를 배포 하는 방법을 보여 줍니다 .이 모듈은 Blob 만들기 및 Blob 삭제에 대 한 이벤트를 Event Grid에 전송 하는 Event Grid 게시자 역할을 합니다.  

IoT Edge Azure Blob Storage에 대 한 개요는 IoT Edge 및 해당 기능 [에 대 한 Azure Blob Storage](../../iot-edge/how-to-store-data-blob.md) 를 참조 하세요.

> [!WARNING]
> Event Grid와 IoT Edge 통합 Azure Blob Storage 미리 보기 상태입니다.

이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** -아직 없는 경우 [무료 계정](https://azure.microsoft.com/free) 을 만듭니다. 
* **장치 Azure IoT Hub 및 IoT Edge** - [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치](../../iot-edge/quickstart.md) 에 대 한 빠른 시작 (아직 없는 경우)의 단계를 따릅니다.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 장치에 모듈을 배포 하는 방법에는 여러 가지가 있으며 모든 장치는 IoT Edge에서 Azure Event Grid에 대해 작동 합니다. 이 문서에서는 Azure Portal에서 IoT Edge에 Event Grid를 배포 하는 단계를 설명 합니다.

>[!NOTE]
> 이 자습서에서는 지 속성 없이 Event Grid 모듈을 배포 합니다. 이는 모듈을 다시 배포할 때이 자습서에서 만든 토픽 및 구독이 삭제 됨을 의미 합니다. 지 속성을 설정 하는 방법에 대 한 자세한 내용은 [Linux에서 상태 유지](persist-state-linux.md) 또는 [Windows에서 지속 상태](persist-state-windows.md)문서를 참조 하세요. 프로덕션 워크 로드의 경우 지 속성을 사용 하 여 Event Grid 모듈을 설치 하는 것이 좋습니다.


### <a name="select-your-iot-edge-device"></a>IoT Edge 장치 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 장치 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택 합니다. 
1. 장치 목록에서 대상 장치의 ID를 클릭 합니다.
1. **모듈 설정**을 선택 합니다. 페이지를 열어 둡니다. 다음 섹션의 단계를 계속 진행 합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 JSON 문서를 수동으로 빌드하는 대신 배포 매니페스트를 만드는 과정을 안내 하는 마법사가 있습니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택 합니다.
1. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공 합니다.

   * **이름**: eventgridmodule
   * **이미지 URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
 1. 다음 섹션을 계속 진행 하 여 배포 하기 전에 Azure Event Grid 구독자 모듈을 추가 합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 HTTP/HTTPs 요청, 클라이언트 인증을 사용 하지 않도록 설정 하는 Event Grid 모듈을 배포 하는 방법을 알아봅니다. 프로덕션 워크 로드의 경우 클라이언트 인증을 사용 하는 HTTPs 요청 및 구독자만 사용 하도록 설정 하는 것이 좋습니다. Event Grid 모듈을 안전 하 게 구성 하는 방법에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid 구독자 IoT Edge 모듈 배포

이 섹션에서는 이벤트를 전달할 수 있는 이벤트 처리기 역할을 하는 다른 IoT 모듈을 배포 하는 방법을 보여 줍니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 다시 선택 합니다. 
1. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
1. 컨테이너의 이름, 이미지 및 컨테이너 만들기 옵션을 제공 합니다.

   * **이름**: 구독자
   * **이미지 URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **컨테이너 만들기 옵션**: 없음
1. 페이지 맨 아래에 있는 **저장**
1. 다음 섹션을 계속 진행 하 여 Azure Blob Storage 모듈을 추가 합니다.

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob Storage 모듈 배포

이 섹션에서는 Azure Blob Storage 모듈을 배포 하는 방법을 보여 줍니다 .이 모듈은 Event Grid 게시자 게시 Blob 생성 및 삭제 이벤트로 작동 합니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택 합니다.
2. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
3. 컨테이너의 이름, 이미지 및 컨테이너 만들기 옵션을 제공 합니다.

   * **이름**: azureblobstorageoniotedge
   * **이미지 URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **컨테이너 만들기 옵션**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob Storage 모듈은 HTTPS와 HTTP를 모두 사용 하 여 이벤트를 게시할 수 있습니다. 
   > - EventGrid에 클라이언트 기반 인증을 사용 하도록 설정한 경우 다음과 같이 EVENTGRID_ENDPOINT 값을 업데이트 하 여 https를 허용 해야 `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` 합니다.
   > - 또한 위의 Json에 다른 환경 변수를 추가 합니다 `AllowUnknownCertificateAuthority=true` . HTTPS를 통해 EventGrid와 통신할 때 **AllowUnknownCertificateAuthority** 는 저장소 모듈이 자체 서명 된 eventgrid 서버 인증서를 신뢰 하도록 허용 합니다.

4. 다음 정보로 복사한 JSON을 업데이트합니다.

   - `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3 자에서 24 자 사이 여야 하 고 소문자와 숫자만 사용 해야 합니다. 공백 없음

   - `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

   - `<event grid module name>`을 Event Grid 모듈의 이름으로 바꿉니다.
   - `<storage mount>`컨테이너 운영 체제에 따라 대체 합니다.
     - Linux 컨테이너의 경우 **내 볼륨:/blobroot**
     - Windows 컨테이너의 경우**내 볼륨: C:/BlobRoot**

5. 페이지 맨 아래에 있는 **저장**
6. **다음** 을 클릭 하 여 경로 섹션으로 이동 합니다.

    > [!NOTE]
    > Azure VM을에 지 장치로 사용 하는 경우이 자습서에서 사용 되는 호스트 포트 (4438, 5888, 8080 및 11002)에서 인바운드 트래픽을 허용 하는 인바운드 포트 규칙을 추가 합니다. 규칙을 추가 하는 방법에 대 한 지침은 [VM에 포트를 여는 방법](../../virtual-machines/windows/nsg-quickstart-portal.md)을 참조 하세요.

### <a name="setup-routes"></a>설정 경로

기본 경로를 유지 하 고 **다음** 을 선택 하 여 검토 섹션으로 이동 합니다.

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에는 이전 섹션에서 선택한 항목에 따라 만든 JSON 배포 매니페스트가 표시 됩니다. **$EdgeAgent**, **$edgeHub**, **eventgridmodule**, **subscriber** 및 **azureblobstorageoniotedge** 의 네 가지 모듈이 모두 배포 되는 것을 확인 합니다.
2. 배포 정보를 검토한 다음 **제출**을 선택합니다.

## <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후 IoT Hub의 IoT Edge 페이지로 돌아갑니다.
2. 배포의 대상으로 지정 된 **IoT Edge 장치** 를 선택 하 여 세부 정보를 엽니다.
3. 장치 세부 정보에서 eventgridmodule, subscriber 및 azureblobstorageoniotedge 모듈이 **배포에 지정** 되어 있고 **장치에서 보고**되는 것으로 표시 되는지 확인 합니다.

   모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated 및 Blobcreated 이벤트 게시

1. 이 모듈은 토픽 **MicrosoftStorage**을 자동으로 만듭니다. 존재 하는지 확인
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    샘플 출력:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용 하는 경우 이전에 지정 된 SAS 키를 헤더로 추가 해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용 하도록 설정 하는 경우 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 구독자는 토픽에 게시 된 이벤트를 등록할 수 있습니다. 이벤트를 수신 하려면 **MicrosoftStorage** 항목에 대 한 Event Grid 구독을 만들어야 합니다.
    1. 다음 콘텐츠를 사용 하 여 blobsubscription.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

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
       > **Endpointtype** 속성은 구독자가 **Webhook**임을 지정 합니다.  **Endpointurl** 은 구독자가 이벤트를 수신 대기 하는 url을 지정 합니다. 이 URL은 이전에 배포한 Azure Function 샘플에 해당 합니다.

    2. 항목에 대 한 구독을 만들려면 다음 명령을 실행 합니다. HTTP 상태 코드가 인지 확인 `200 OK` 합니다.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용 하는 경우 이전에 지정 된 SAS 키를 헤더로 추가 해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용 하도록 설정 하는 경우 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 다음 명령을 실행 하 여 구독이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       샘플 출력:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용 하는 경우 이전에 지정 된 SAS 키를 헤더로 추가 해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용 하도록 설정 하는 경우 말아 넘기기 요청은 다음과 같습니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 다운로드 하 여 [로컬 저장소에 연결](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>이벤트 배달 확인

### <a name="verify-blobcreated-event-delivery"></a>BlobCreated 이벤트 배달 확인

1. Azure Storage 탐색기에서 로컬 저장소에 블록 blob으로 파일을 업로드 하면 모듈에서 자동으로 만들기 이벤트를 게시 합니다. 
2. 만든 이벤트에 대 한 구독자 로그를 확인 합니다. [이벤트 배달을 확인](pub-sub-events-webhook-local.md#verify-event-delivery) 하는 단계를 수행 합니다.

    예제 출력:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>BlobDeleted 이벤트 배달 확인

1. Azure Storage 탐색기를 사용 하 여 로컬 저장소에서 blob을 삭제 하면 모듈이 자동으로 삭제 이벤트를 게시 합니다. 
2. Delete 이벤트에 대 한 구독자 로그를 확인 합니다. [이벤트 배달을 확인](pub-sub-events-webhook-local.md#verify-event-delivery) 하는 단계를 수행 합니다.

    예제 출력:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

지금까지 자습서를 완료 했습니다. 다음 섹션에서는 이벤트 속성에 대 한 세부 정보를 제공 합니다.

### <a name="event-properties"></a>이벤트 속성

다음은 지원 되는 이벤트 속성과 해당 유형 및 설명 목록입니다. 

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| api | string | 이벤트를 트리거하는 작업입니다. 다음 값 중 하나일 수 있습니다. <ul><li>BlobCreated-허용 되는 값은 및입니다. `PutBlob``PutBlockList`</li><li>BlobDeleted-허용 되는 값은 `DeleteBlob` , `DeleteAfterUpload` 및 `AutoDelete` 입니다. <p>`DeleteAfterUpload`DeleteAfterUpload desired 속성이 true로 설정 되어 있으므로 blob이 자동으로 삭제 되 면 이벤트가 생성 됩니다. </p><p>`AutoDelete`deleteAfterMinutes desired 속성 값이 만료 되어 blob이 자동으로 삭제 되 면 이벤트가 생성 됩니다.</p></li></ul>|
| clientRequestId | string | 저장소 API 작업에 대 한 클라이언트 제공 요청 ID입니다. 이 ID는 로그의 "클라이언트-요청 id" 필드를 사용 하 여 Azure Storage 진단 로그와 상호 연결 하는 데 사용할 수 있으며, "x-y-id" 헤더를 사용 하 여 클라이언트 요청에 제공할 수 있습니다. 자세한 내용은 [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조 하세요. |
| requestId | 문자열 | 저장소 API 작업에 대 한 서비스 생성 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | string | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | string | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | integer | Blob의 크기(바이트)입니다. |
| blobType | string | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| url | 문자열 | Blob에 대한 경로입니다. <br>클라이언트에서 REST API Blob을 사용 하는 경우 url의 구조는 * \<storage-account-name\> . blob.core.windows.net/ \<container-name\> / \<file-name\> *입니다. <br>클라이언트에서 Data Lake Storage REST API를 사용 하는 경우 url의 구조는 * \<storage-account-name\> . dfs.core.windows.net/ \<file-system-name\> / \<file-name\> *입니다. |


## <a name="next-steps"></a>다음 단계

Blob Storage 설명서에서 다음 문서를 참조 하세요.

- [Blob Storage 이벤트 필터링](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Blob Storage 이벤트 사용에 대 한 권장 방법](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

이 자습서에서는 Azure Blob Storage에서 blob을 만들거나 삭제 하 여 이벤트를 게시 했습니다. 클라우드로 이벤트를 전달 하는 방법에 대 한 자세한 내용은 다른 자습서 (Azure 이벤트 허브 또는 Azure IoT Hub)를 참조 하세요. 

- [Azure Event Grid로 이벤트 전달](forward-events-event-grid-cloud.md)
- [Azure IoT Hub로 이벤트 전달](forward-events-iothub.md)
