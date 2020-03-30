---
title: 리눅스에서 상태 유지 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: Linux에서 메타데이터 유지
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086661"
---
# <a name="persist-state-in-linux"></a>Linux에서 상태 유지

이벤트 그리드 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장됩니다. 지속성 이 없으면 모듈이 다시 배포되면 생성된 모든 메타데이터가 손실됩니다. 배포 및 다시 시작 간에 데이터를 보존하려면 컨테이너 파일 시스템 외부에 데이터를 유지해야 합니다.

기본적으로 메타데이터만 유지되고 이벤트는 성능 향상을 위해 메모리에 저장됩니다. 지속 이벤트 섹션에 따라 이벤트 지속성도 활성화합니다.

이 문서에서는 Linux 배포에서 지속성을 가진 이벤트 그리드 모듈을 배포하는 단계를 제공합니다.

> [!NOTE]
>이벤트 그리드 모듈은 UID `2000` 및 이름을 `eventgriduser`가진 권한이 낮은 사용자로 실행됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 마운트를 통한 지속성

 [Docker 볼륨은](https://docs.docker.com/storage/volumes/) 배포 전체에서 데이터를 보존하는 데 사용됩니다. Docker가 Event Grid 모듈 배포의 일부로 명명된 볼륨을 자동으로 생성하도록 할 수 있습니다. 이 옵션은 가장 간단한 옵션입니다. 다음과 같이 바인딩 섹션에서 작성할 볼륨 이름을 지정할 수 **있습니다.**

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
>바인드 값의 두 번째 부분을 변경하지 마십시오. 모듈 내의 특정 위치를 가리킵니다. Linux의 이벤트 그리드 모듈의 경우 **/app/metadataDb여야**합니다.

예를 들어 다음 구성으로 인해 메타데이터가 유지되는 볼륨 **egmetadataDbVol이** 생성됩니다.

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

볼륨을 탑재하는 대신 호스트 시스템에서 디렉터리를 만들고 해당 디렉터리를 마운트할 수 있습니다.

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 마운트를 통한 지속성

docker 볼륨 대신 호스트 폴더를 탑재할 수도 있습니다.

1. 먼저 다음 명령을 실행하여 호스트 컴퓨터에서 이름 **eventgriduser** 및 ID **2000을** 사용하여 사용자를 만듭니다.

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 다음 명령을 실행 하여 호스트 파일 시스템에서 디렉터리를 만듭니다.

   ```sh
   md <your-directory-name-here>
   ```

    예를 들어 다음 명령을 실행하면 **myhostdir**라는 디렉터리가 생성 됩니다.

    ```sh
    md /myhostdir
    ```
1. 다음으로 다음 명령을 실행하여 이 폴더의 **eventgriduser** 소유자를 만듭니다.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    예를 들면 다음과 같습니다.

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **바인딩을** 사용하여 디렉터리를 마운트하고 Azure 포털에서 이벤트 그리드 모듈을 다시 배포합니다.

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

    예를 들면 다음과 같습니다.

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
    >바인드 값의 두 번째 부분을 변경하지 마십시오. 모듈 내의 특정 위치를 가리킵니다. 리눅스에서 이벤트 그리드 모듈의 경우 **/app/metadataDb** 및 **/app/eventsDb여야** 합니다.


## <a name="persist-events"></a>이벤트 유지

이벤트 지속성을 사용하려면 먼저 위의 섹션을 사용하여 볼륨 마운트 또는 호스트 디렉터리 마운트를 통해 메타데이터 지속성을 사용하도록 설정해야 합니다.

지속 이벤트에 대해 주의해야 할 중요한 사항:

* 지속 이벤트는 이벤트 구독별로 활성화되며 볼륨 또는 디렉터리가 탑재되면 옵트인됩니다.
* 이벤트 지속성은 생성 시 이벤트 구독에서 구성되며 이벤트 구독이 생성된 후에는 수정할 수 없습니다. 이벤트 지속성을 전환하려면 이벤트 구독을 삭제하고 다시 만들어야 합니다.
* 지속 이벤트는 메모리 작업보다 거의 항상 느리지만 속도 차이는 드라이브의 특성에 따라 크게 달라집니다. 속도와 안정성 사이의 장단점은 모든 메시징 시스템에 내재되어 있지만 일반적으로 대규모로만 눈에 띄게 됩니다.

이벤트 구독에서 이벤트 지속성을 활성화하려면 `true`다음으로 설정합니다. `persistencePolicy`

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
