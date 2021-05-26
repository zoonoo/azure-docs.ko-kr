---
title: Windows에서 상태 유지 - Azure Event Grid IoT Edge | Microsoft Docs
description: Windows에서 상태 유지
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 23428688d7f65bf311ecd23d47a23ab9b2e89617
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367747"
---
# <a name="persist-state-in-windows"></a>Windows에서 상태 유지

Event Grid 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장됩니다. 지속성이 없으면 모듈을 다시 배포할 경우 만들어진 메타데이터가 모두 손실됩니다. 다시 배포하거나 다시 시작해도 데이터를 보존하려면 컨테이너 파일 시스템 외부에 데이터를 유지해야 합니다. 

기본적으로 메타데이터만 유지되며 성능 향상을 위해 이벤트는 계속 메모리 내에 저장됩니다. 이벤트 유지 섹션에 따라 이벤트 지속성도 사용하도록 설정하세요.

이 문서에는 Windows 배포에서 지속성 있는 Event Grid 모듈을 배포하는 데 필요한 단계가 제공되어 있습니다.

> [!NOTE]
>Event Grid 모듈은 Windows에서 권한이 낮은 사용자 **ContainerUser** 로 실행됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 탑재를 통해 지속성 지원

[Docker 볼륨](https://docs.docker.com/storage/volumes/)은 배포 간에 데이터를 유지하는 데 사용됩니다. 볼륨을 탑재하려면 docker 명령을 사용하여 볼륨을 만들고, 컨테이너에서 이를 모듈을 읽고, 모듈에 쓰고, 모듈을 배포할 수 있도록 권한을 부여해야 합니다.

1. 다음 명령을 실행하여 볼륨을 만듭니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    예제:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 아래 명령을 실행하여 볼륨이 매핑되는 호스트 디렉터리를 가져옵니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    예제:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   샘플 출력:

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 다음과 같이 **사용자** 그룹을 **Mountpoint** 가 가리키는 값에 추가합니다.
    1. 파일 탐색기를 시작합니다.
    1. **Mountpoint** 가 가리키는 폴더로 이동합니다.
    1. 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
    1. **보안** 을 선택합니다.
    1. *그룹 또는 사용자 이름 아래에서 **편집** 을 선택합니다.
    1. **추가** 를 선택하고 `Users`, **이름 확인** 을 선택한 후 **확인** 을 선택합니다.
    1. *사용자의 사용 권한* 아래에서 **수정** 을 선택하고 **확인** 을 선택합니다.
1. **바인딩** 을 사용하여 이 볼륨을 탑재하고 Azure Portal에서 Event Grid 모듈을 다시 배포합니다.

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >바인드 값의 두 번째 부분을 변경하지 마세요. 모듈의 특정 위치를 가리킵니다. Windows의 Event Grid 모듈은 **C:\\app\\metadataDb** 여야 합니다.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 탑재를 통해 지속성 지원

볼륨을 탑재하는 대신 호스트 시스템에 디렉터리를 만들어 해당 디렉터리를 탑재할 수 있습니다.

1. 다음 명령을 실행하여 호스트 파일 시스템에 디렉터리를 만듭니다.

   ```sh
   mkdir <your-directory-name-here>
   ```

   예제:

   ```sh
   mkdir C:\myhostdir
   ```
1. **Binds** 를 사용하여 디렉터리를 탑재하고 Azure Portal에서 Event Grid 모듈을 다시 배포합니다.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >바인드 값의 두 번째 부분을 변경하지 마세요. 모듈의 특정 위치를 가리킵니다. Windows의 Event Grid 모듈은 **C:\\app\\metadataDb** 여야 합니다.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>이벤트 유지

이벤트 지속성을 사용하도록 설정하려면 먼저 위의 섹션에 있는 볼륨 탑재 또는 호스트 디렉터리 탑재를 통해 이벤트 지속성을 사용하도록 설정해야 합니다.

이벤트 유지에서 유의해야 할 중요한 사항은 다음과 같습니다.

* 이벤트 유지는 개별 이벤트 구독을 기준으로 사용하도록 설정되며 볼륨 또는 디렉터리가 탑재되면 옵트인됩니다.
* 이벤트 지속성은 이벤트 구독에서 만들 때 구성되며 이벤트 구독을 만든 후에는 이벤트 지속성을 수정할 수 없습니다. 이벤트 지속성을 토글하려면 이벤트 구독을 삭제하고 다시 만들어야 합니다.
* 이벤트 유지는 거의 항상 메모리 내 작업보다 느리지만 속도 차이는 주로 드라이브의 특성에 따라 달라집니다. 속도와 안정성 간 절충은 모든 메시징 시스템에 해당되지만 스케일이 큰 경우에만 나타납니다.

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