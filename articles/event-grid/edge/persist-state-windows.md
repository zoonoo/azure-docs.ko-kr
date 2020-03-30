---
title: Windows의 상태 유지 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: Windows에서 상태 유지
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086618"
---
# <a name="persist-state-in-windows"></a>Windows에서 상태 유지

이벤트 그리드 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장됩니다. 지속성 이 없으면 모듈이 다시 배포되면 생성된 모든 메타데이터가 손실됩니다. 배포 및 다시 시작 간에 데이터를 보존하려면 컨테이너 파일 시스템 외부에 데이터를 유지해야 합니다. 

기본적으로 메타데이터만 유지되고 이벤트는 성능 향상을 위해 메모리에 저장됩니다. 지속 이벤트 섹션에 따라 이벤트 지속성도 활성화합니다.

이 문서에서는 Windows 배포에서 지속성을 가진 Event Grid 모듈을 배포하는 데 필요한 단계를 제공합니다.

> [!NOTE]
>이벤트 그리드 모듈은 Windows에서 권한이 낮은 사용자 **컨테이너사용자로** 실행됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 마운트를 통한 지속성

[Docker 볼륨은](https://docs.docker.com/storage/volumes/) 배포 전체에서 데이터를 보존하는 데 사용됩니다. 볼륨을 탑재하려면 docker 명령을 사용하여 볼륨을 만들고 컨테이너가 모듈을 읽고 쓸 수 있도록 권한을 부여해야 합니다.

1. 다음 명령을 실행하여 볼륨을 만듭니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    예를 들면 다음과 같습니다.

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 아래 명령을 실행하여 볼륨이 매핑되는 호스트 디렉토리를 가져옵니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    예를 들면 다음과 같습니다.

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   샘플 출력: -

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
1. 다음과 같이 **Mountpoint가** 가리키는 값에 **사용자** 그룹을 추가합니다.
    1. 파일 탐색기를 시작합니다.
    1. **마운트포인트가**가리키는 폴더로 이동합니다.
    1. 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.
    1. **보안**을 선택합니다.
    1. *그룹 또는 사용자 이름 아래에서 **편집을**선택합니다.
    1. **추가**, `Users`입력, **이름 선택을**선택하고 **확인을**선택합니다.
    1. *사용자에 대한 사용 권한에서* **수정을**선택하고 **확인을**선택합니다.
1. **바인딩을** 사용하여 이 볼륨을 마운트하고 Azure 포털에서 이벤트 그리드 모듈을 다시 배포합니다.

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
   >바인드 값의 두 번째 부분을 변경하지 마십시오. 모듈의 특정 위치를 가리킵니다. 창에서 이벤트 그리드 모듈의 경우 **\\C:\\앱 메타데이터DB여야**합니다.


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

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 마운트를 통한 지속성

볼륨을 탑재하는 대신 호스트 시스템에서 디렉터리를 만들고 해당 디렉터리를 마운트할 수 있습니다.

1. 다음 명령을 실행 하여 호스트 파일 시스템에서 디렉터리를 만듭니다.

   ```sh
   mkdir <your-directory-name-here>
   ```

   예를 들면 다음과 같습니다.

   ```sh
   mkdir C:\myhostdir
   ```
1. **바인딩을** 사용하여 디렉터리를 마운트하고 Azure 포털에서 이벤트 그리드 모듈을 다시 배포합니다.

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
    >바인드 값의 두 번째 부분을 변경하지 마십시오. 모듈의 특정 위치를 가리킵니다. 창의 이벤트 그리드 모듈의 경우 **C:\\\\앱 메타데이터DB여야**합니다.

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

이벤트 지속성을 사용하려면 먼저 위의 섹션을 사용하여 볼륨 마운트 또는 호스트 디렉터리 마운트를 통해 이벤트 지속성을 활성화해야 합니다.

지속 이벤트에 대해 주의해야 할 중요한 사항:

* 지속 이벤트는 이벤트 구독별로 활성화되며 볼륨 또는 디렉터리가 탑재되면 옵트인됩니다.
* 이벤트 지속성은 생성 시 이벤트 구독에서 구성되며 이벤트 구독이 생성된 후에는 수정할 수 없습니다. 이벤트 지속성을 전환하려면 이벤트 구독을 삭제하고 다시 만들어야 합니다.
* 지속 이벤트는 메모리 작업보다 거의 항상 느리지만 속도 차이는 드라이브의 특성에 따라 크게 달라집니다. 속도와 안정성 사이의 장단점은 모든 메시징 시스템에 내재되어 있지만 대규모로만 눈에 띄게 됩니다.

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