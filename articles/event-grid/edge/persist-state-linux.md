---
title: Linux에서 상태 유지-Azure Event Grid IoT Edge | Microsoft Docs
description: Linux에서 메타 데이터 유지
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100343"
---
# <a name="persist-state-in-linux"></a>Linux에서 상태 유지

Event Grid 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장 됩니다. 지 속성을 사용 하지 않으면 모듈을 다시 배포 하면 생성 된 모든 메타 데이터가 손실 됩니다. 현재 메타 데이터만 유지 됩니다. 이벤트는 메모리에 저장 됩니다. 모듈을 다시 배포 하거나 다시 시작 하는 Event Grid 경우에는 배달 되지 않은 이벤트가 모두 손실 됩니다.

이 문서에서는 Linux 배포에서 지 속성을 사용 하 여 Event Grid 모듈을 배포 하는 단계를 제공 합니다.

> [!NOTE]
>Event Grid 모듈은 UID `2000` 및 이름 `eventgriduser`을 사용 하 여 권한이 낮은 사용자로 실행 됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 탑재를 통한 지 속성

 [Docker 볼륨](https://docs.docker.com/storage/volumes/) 은 배포 간에 데이터를 유지 하는 데 사용 됩니다. Event Grid 모듈을 배포 하는 과정에서 docker가 명명 된 볼륨을 자동으로 만들도록 할 수 있습니다. 이 옵션은 가장 간단한 옵션입니다. 다음과 같이 **바인드** 섹션에서 만들 볼륨 이름을 지정할 수 있습니다.

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Bind 값의 두 번째 부분을 변경 하지 마십시오. 모듈 내의 특정 위치를 가리킵니다. Linux의 Event Grid 모듈은 **/app/metadataDb**이어야 합니다.

예를 들어 다음 구성을 수행 하면 메타 데이터가 유지 되는 볼륨 **egmetadataDbVol** 생성 됩니다.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
    ],
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

또는 docker 클라이언트 명령을 사용 하 여 docker 볼륨을 만들 수 있습니다. 

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 탑재를 통한 지 속성

Docker 볼륨 대신 호스트 폴더를 탑재 하는 옵션도 있습니다.

1. 먼저 다음 명령을 실행 하 여 호스트 컴퓨터에서 **eventgriduser** 및 ID **2000** 이름을 가진 사용자를 만듭니다.

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 다음 명령을 실행 하 여 호스트 파일 시스템에 디렉터리를 만듭니다.

   ```sh
   md <your-directory-name-here>
   ```

    예를 들어 다음 명령을 실행 하면 **myhostdir**이라는 디렉터리가 만들어집니다.

    ```sh
    md /myhostdir
    ```
1. 다음 명령을 실행 하 여이 폴더의 **eventgriduser** 소유자를 만듭니다.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    예를 들면 다음과 같습니다.

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **바인딩을** 사용 하 여 디렉터리를 탑재 하 고 Azure Portal에서 Event Grid 모듈을 다시 배포 합니다.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    예를 들면 다음과 같습니다.

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
                ],
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

    >[!IMPORTANT]
    >Bind 값의 두 번째 부분을 변경 하지 마십시오. 모듈 내의 특정 위치를 가리킵니다. Linux의 Event Grid 모듈은 **/app/metadata**이어야 합니다.
