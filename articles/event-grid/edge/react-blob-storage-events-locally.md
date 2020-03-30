---
title: Blob 저장소 모듈 이벤트에 반응 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: Blob 저장소 모듈 이벤트에 반응
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086598"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>자습서: IoT 에지의 Blob 저장소 이벤트에 반응(미리 보기)
이 문서에서는 이벤트 그리드 게시자 역할을 하는 Azure Blob Storage를 IoT 모듈에 배포하는 방법을 보여 주며, 이 모듈은 Blob 생성 및 Blob 삭제에 대한 이벤트를 이벤트 그리드로 전송합니다.  

IoT 에지의 Azure Blob 저장소에 대한 개요는 [IoT 에지의 Azure Blob 저장소](../../iot-edge/how-to-store-data-blob.md) 및 해당 기능을 참조하세요.

> [!WARNING]
> 이벤트 그리드와의 IoT 에지 통합에 대한 Azure Blob 스토리지가 미리 보기 상태입니다.

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

 1. **저장**을 클릭합니다.
 1. 다음 섹션으로 계속 하여 Azure 이벤트 그리드 구독자 모듈을 함께 배포하기 전에 추가합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 HTTP/HTTP 요청, 클라이언트 인증을 모두 사용할 수 있도록 이벤트 그리드 모듈을 배포하는 방법을 배웁니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하도록 설정한 HTTP 요청 및 구독자만 사용하도록 설정하는 것이 좋습니다. 이벤트 그리드 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오.
    

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
1. Azure Blob 저장소 모듈을 추가하려면 다음 섹션으로 계속 이동합니다.

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob 저장소 배포 모듈

이 섹션에서는 Blob생성 및 삭제된 이벤트를 게시하는 이벤트 그리드 게시자 역할을 하는 Azure Blob Storage 모듈을 배포하는 방법을 보여 주며, 이 모듈을 배포하는 방법을 보여 주시습니다.

### <a name="add-modules"></a>모듈 추가

1. 배포 **모듈** 섹션에서 **추가**
2. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈을** 선택합니다.
3. 컨테이너의 이름, 이미지 및 컨테이너 만들기 옵션을 제공합니다.

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
   > - Blob Storage 모듈은 HTTPS 및 HTTP를 모두 사용하여 이벤트를 게시할 수 있습니다. 
   > - EventGrid에 대한 클라이언트 기반 인증을 사용하도록 설정한 경우 다음과 같이 https를 허용하도록 `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`EVENTGRID_ENDPOINT 값을 업데이트해야 합니다.
   > - 또한 위의 Json에 다른 환경 변수를 `AllowUnknownCertificateAuthority=true` 추가합니다. HTTPS를 통해 EventGrid와 대화할 때 **AllowUnknownCertificateAuthority를** 사용하면 저장소 모듈이 자체 서명된 EventGrid 서버 인증서를 신뢰할 수 있습니다.

4. 다음 정보로 복사한 JSON을 업데이트합니다.

   - `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3~24자이어야 하며 소문자와 숫자는 있어야 합니다. 공백 없음

   - `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

   - 이벤트 `<event grid module name>` 그리드 모듈의 이름으로 바꿉니다.
   - 컨테이너 `<storage mount>` 운영 체제에 따라 교체하십시오.
     - 리눅스 컨테이너에 대 한, **내 볼륨:/blobroot**
     - Windows 컨테이너의 경우**내 볼륨:C:/BlobRoot**

5. **저장**을 클릭합니다.
6. **다음을** 클릭하여 경로 섹션을 계속합니다.

    > [!NOTE]
    > Azure VM을 에지 장치로 사용하는 경우 인바운드 포트 규칙을 추가하여 이 자습서에 사용된 호스트 포트(4438, 5888, 8080 및 11002)에서 인바운드 트래픽을 허용합니다. 규칙 추가에 대한 지침은 [VM에 대한 포트를 여는 방법을](../../virtual-machines/windows/nsg-quickstart-portal.md)참조하십시오.

### <a name="setup-routes"></a>설정 경로

기본 경로를 유지하고 **다음을** 선택하여 검토 섹션으로 계속

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에는 이전 섹션의 선택에 따라 만들어진 JSON 배포 매니페스트가 표시됩니다. **$edgeAgent,** **$edgeHub,** **eventgridmodule,** **구독자** 및 **azureblobstorageoniotedge** 모든 배포 되는 다음 4 개의 모듈을 참조 합니다.
2. 배포 정보를 검토한 다음 **제출**을 선택합니다.

## <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후 IoT Hub의 IoT Edge 페이지로 돌아갑니다.
2. 배포를 대상으로 한 **IoT Edge 장치를** 선택하여 세부 정보를 엽니다.
3. 장치 세부 정보에서 eventgridmodule, 구독자 및 azureblobstorageoniotedge 모듈이 **배포에 지정되고** **장치에 의해 보고됨으로**나열되어 있는지 확인합니다.

   모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>게시 BlobCreated 및 BlobDeleted 이벤트

1. 이 모듈은 자동으로 항목 **마이크로 소프트 스토리지를**만듭니다 . 있는지 확인합니다.
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
    > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하도록 설정한 경우 이전에 지정한 SAS 키가 헤더로 추가되어야 합니다. 따라서 컬 요청은 다음과 됩니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정된 경우 curl 요청은 다음과 같은 것입니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 구독자는 토픽에 게시된 이벤트에 등록할 수 있습니다. 이벤트를 받으려면 **MicrosoftStorage** 항목에 대한 이벤트 그리드 구독을 만들어야 합니다.
    1. 다음 콘텐츠로 blobsubscription.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

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
       > **endpointType** 속성은 구독자가 **Webhook입니다.**  **endpointUrl은** 구독자가 이벤트를 수신 대기하는 URL을 지정합니다. 이 URL은 이전에 배포한 Azure Function 샘플에 해당합니다.

    2. 다음 명령을 실행하여 토픽에 대한 구독을 만듭니다. HTTP 상태 코드가 있는지 `200 OK`확인합니다.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하도록 설정한 경우 이전에 지정한 SAS 키가 헤더로 추가되어야 합니다. 따라서 컬 요청은 다음과 됩니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정된 경우 curl 요청은 다음과 같은 것입니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

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
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하도록 설정한 경우 이전에 지정한 SAS 키가 헤더로 추가되어야 합니다. 따라서 컬 요청은 다음과 됩니다.`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정된 경우 curl 요청은 다음과 같은 것입니다.`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure 저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/) 다운로드하여 [로컬 저장소에 연결합니다.](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>이벤트 전달 확인

### <a name="verify-blobcreated-event-delivery"></a>Blob생성 된 이벤트 배달 확인

1. Azure Storage 탐색기에서 로컬 저장소에 블록 Blob으로 파일을 업로드 하 고 모듈은 자동으로 create 이벤트를 게시 합니다. 
2. 생성 이벤트에 대한 구독자 로그를 확인합니다. 단계에 따라 [이벤트 게재를 확인합니다.](pub-sub-events-webhook-local.md#verify-event-delivery)

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

1. Azure 저장소 탐색기를 사용하여 로컬 저장소에서 Blob을 삭제하면 모듈이 삭제 이벤트를 자동으로 게시합니다. 
2. 삭제 이벤트에 대한 구독자 로그를 확인합니다. 단계에 따라 [이벤트 게재를 확인합니다.](pub-sub-events-webhook-local.md#verify-event-delivery)

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

축하합니다! 자습서를 완료했습니다. 다음 섹션에서는 이벤트 속성에 대한 세부 정보를 제공합니다.

### <a name="event-properties"></a>이벤트 속성

지원되는 이벤트 속성 및 해당 유형 및 설명 목록은 다음과 같습니다. 

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| api | 문자열 | 이벤트를 트리거하는 작업입니다. 다음 값 중 하나일 수 있습니다. <ul><li>BlobCreated - 허용된 `PutBlob` 값은 다음과 같습니다.`PutBlockList`</li><li>BlobDeleted - 허용된 `DeleteAfterUpload` `AutoDelete`값은 및 을 입니다. `DeleteBlob` <p>deleteAfterUpload 원하는 속성이 true로 설정되어 있기 때문에 Blob이 자동으로 삭제될 때 `DeleteAfterUpload` 이벤트가 생성됩니다. </p><p>`AutoDelete`삭제후분 원하는 속성 값이 만료되어 Blob이 자동으로 삭제될 때 이벤트가 생성됩니다.</p></li></ul>|
| clientRequestId | 문자열 | 저장소 API 작업에 대한 클라이언트 제공 요청 ID입니다. 이 ID는 로그의 "클라이언트-요청-id" 필드를 사용하여 Azure Storage 진단 로그와 상관 관계를 지정하는 데 사용할 수 있으며 "x-ms-client-request-id" 헤더를 사용하여 클라이언트 요청에 제공할 수 있습니다. 자세한 내용은 [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조하십시오. |
| requestId | 문자열 | 저장소 API 작업에 대한 서비스 생성 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | integer | Blob의 크기(바이트)입니다. |
| blobType | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| url | 문자열 | Blob에 대한 경로입니다. <br>클라이언트가 Blob REST API를 사용하는 경우 URL에는 * \<저장소-계정\>이름\<.blob.core.windows.net/\>/\<컨테이너 이름\>파일 이름입니다.* <br>클라이언트가 Data Lake Storage REST API를 사용하는 경우 URL에는 * \<저장소-계정\>\<이름 .dfs.core.windows.net/\>/\<파일 시스템\>이름 파일 이름입니다.* |


## <a name="next-steps"></a>다음 단계

Blob 저장소 설명서의 다음 문서를 참조하십시오.

- [필터 Blob 저장소 이벤트](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Blob 저장소 이벤트를 사용하는 데 권장되는 방법](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

이 자습서에서는 Azure Blob 저장소에서 Blob을 만들거나 삭제하여 이벤트를 게시했습니다. 이벤트를 클라우드(Azure 이벤트 허브 또는 Azure IoT Hub)로 전달하는 방법을 알아보려면 다른 자습서를 참조하십시오. 

- [Azure Event Grid로 이벤트 전달](forward-events-event-grid-cloud.md)
- [Azure IoT Hub로 이벤트 전달](forward-events-iothub.md)
