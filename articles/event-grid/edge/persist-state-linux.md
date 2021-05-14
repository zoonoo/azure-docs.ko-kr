---
title: Linux에서 상태 유지 - Azure Event Grid IoT Edge | Microsoft Docs
description: Linux에서 메타데이터 유지
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171485"
---
# <a name="persist-state-in-linux"></a>Linux에서 상태 유지

Event Grid 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장됩니다. 지속성이 없으면 모듈을 다시 배포할 경우 만들어진 메타데이터가 모두 손실됩니다. 다시 배포하거나 다시 시작해도 데이터를 보존하려면 컨테이너 파일 시스템 외부에 데이터를 유지해야 합니다.

기본적으로 메타데이터만 유지되며 성능 향상을 위해 이벤트는 계속 메모리 내에 저장됩니다. 이벤트 유지 섹션에 따라 이벤트 지속성도 사용하도록 설정하세요.

이 문서에는 Linux 배포에서 지속성 있는 Event Grid 모듈을 배포하는 단계가 제공되어 있습니다.

> [!NOTE]
>Event Grid 모듈은 UID가 `2000`이고 이름이 `eventgriduser`인 권한이 낮은 사용자로 실행됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 탑재를 통해 지속성 지원

 배포 간에 데이터를 유지하는 데는 [Docker 볼륨](https://docs.docker.com/storage/volumes/)이 사용됩니다. Event Grid 모듈 배포의 일부로 Docker에서 명명된 볼륨을 자동으로 만들도록 할 수 있습니다. 이 옵션이 가장 간단한 옵션입니다. 다음과 같이 **Binds** 섹션에 만들어지는 볼륨 이름을 지정할 수 있습니다.

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
>바인드 값의 두 번째 부분을 변경하지 마세요. 이 부분은 모듈 내의 특정 위치를 가리킵니다. Linux의 Event Grid 모듈은 **/app/metadataDb** 가 되어야 합니다.

예를 들어 다음 구성을 수행하면 메타데이터를 유지할 **egmetadataDbVol** 볼륨이 만들어집니다.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

볼륨을 탑재하는 대신 호스트 시스템에 디렉터리를 만들어 해당 디렉터리를 탑재할 수 있습니다.

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 탑재를 통해 지속성 지원

Docker 볼륨 대신 호스트 폴더를 탑재하는 옵션도 있습니다.

1. 먼저 다음 명령을 실행하여 호스트 머신에서 이름이 **eventgriduser** 이고 ID가 **2000** 인 사용자를 만듭니다.

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 다음 명령을 실행하여 호스트 파일 시스템에 디렉터리를 만듭니다.

   ```sh
   md <your-directory-name-here>
   ```

    예를 들어 다음 명령을 실행하면 **myhostdir** 이라는 디렉터리가 만들어집니다.

    ```sh
    md /myhostdir
    ```
1. 다음 명령을 실행하여 이 폴더의 **eventgriduser** 소유자를 만듭니다.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    예제:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **Binds** 를 사용하여 디렉터리를 탑재하고 Azure Portal에서 Event Grid 모듈을 다시 배포합니다.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    예제:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >바인드 값의 두 번째 부분을 변경하지 마세요. 이 부분은 모듈 내의 특정 위치를 가리킵니다. Linux의 Event Grid 모듈은 **/app/metadataDb** 및 **/app/eventsDb** 가 되어야 합니다.


## <a name="persist-events"></a>이벤트 유지

이벤트 지속성을 사용하도록 설정하려면 먼저 위의 섹션에 있는 볼륨 탑재 또는 호스트 디렉터리 탑재를 통해 메타데이터 지속성을 사용하도록 설정해야 합니다.

이벤트 유지에서 유의해야 할 중요한 사항은 다음과 같습니다.

* 이벤트 유지는 개별 이벤트 구독을 기준으로 사용하도록 설정되며 볼륨 또는 디렉터리가 탑재되면 옵트인됩니다.
* 이벤트 지속성은 이벤트 구독에서 만들 때 구성되며 이벤트 구독을 만든 후에는 이벤트 지속성을 수정할 수 없습니다. 이벤트 지속성을 토글하려면 이벤트 구독을 삭제하고 다시 만들어야 합니다.
* 이벤트 유지는 거의 항상 메모리 내 작업보다 느리지만 속도 차이는 주로 드라이브의 특성에 따라 달라집니다. 속도와 안정성 간 절충은 모든 메시징 시스템에 해당되지만 일반적으로 스케일이 큰 경우에만 나타납니다.

이벤트 구독에서 이벤트 지속성을 사용하도록 설정하려면 `persistencePolicy`를 `true`로 설정합니다.

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
