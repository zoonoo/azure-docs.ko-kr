---
title: Windows에서 상태 유지-Azure Event Grid IoT Edge | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086618"
---
# <a name="persist-state-in-windows"></a>Windows에서 상태 유지

Event Grid 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장 됩니다. 지 속성을 사용 하지 않으면 모듈을 다시 배포 하면 생성 된 모든 메타 데이터가 손실 됩니다. 배포 및 다시 시작 간에 데이터를 유지 하려면 컨테이너 파일 시스템 외부에서 데이터를 유지 해야 합니다. 

기본적으로 메타 데이터는 유지 되 고 성능 향상을 위해 이벤트가 여전히 메모리 내에 저장 됩니다. 이벤트 지 속성을 사용 하려면 지속 이벤트 섹션을 따르세요.

이 문서에서는 Windows 배포에서 지 속성을 사용 하 여 Event Grid 모듈을 배포 하는 데 필요한 단계를 제공 합니다.

> [!NOTE]
>Event Grid 모듈은 Windows에서 권한이 낮은 사용자 **ContainerUser** 실행 됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 탑재를 통한 지 속성

[Docker 볼륨](https://docs.docker.com/storage/volumes/) 은 배포 간에 데이터를 유지 하는 데 사용 됩니다. 볼륨을 탑재 하려면 docker 명령을 사용 하 여 볼륨을 만들고, 컨테이너에서이를 읽고 쓰고, 모듈을 배포할 수 있도록 권한을 부여 해야 합니다.

1. 다음 명령을 실행 하 여 볼륨을 만듭니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    예를 들면 다음과 같습니다.

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 아래 명령을 실행 하 여 볼륨이 매핑되는 호스트 디렉터리를 가져옵니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    예를 들면 다음과 같습니다.

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   샘플 출력:-

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
1. 다음과 같이 **사용자** 그룹을 **탑재 지점을** 가리키는 값에 추가 합니다.
    1. 파일 탐색기를 시작 합니다.
    1. **탑재 지점을**가리키는 폴더로 이동 합니다.
    1. 를 마우스 오른쪽 단추로 클릭 한 다음 **속성**을 선택 합니다.
    1. **보안**을 선택 합니다.
    1. * 그룹 또는 사용자 이름 아래에서 **편집**을 선택 합니다.
    1. **추가**를 선택 하 `Users`고, **이름을**입력 하 고, 확인 **을 선택 합니다.**
    1. *사용자의 사용 권한*아래에서 **수정**을 선택 하 고 **확인**을 선택 합니다.
1. **바인딩을** 사용 하 여이 볼륨을 탑재 하 고 Azure Portal에서 Event Grid 모듈을 다시 배포 합니다.

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
   >Bind 값의 두 번째 부분을 변경 하지 마십시오. 모듈의 특정 위치를 가리킵니다. Windows의 Event Grid 모듈은 **C:\\app\\metadataDb**여야 합니다.


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

## <a name="persistence-via-host-directory-mount"></a>호스트 디렉터리 탑재를 통한 지 속성

볼륨을 탑재 하는 대신 호스트 시스템에 디렉터리를 만들고 해당 디렉터리를 탑재할 수 있습니다.

1. 다음 명령을 실행 하 여 호스트 파일 시스템에 디렉터리를 만듭니다.

   ```sh
   mkdir <your-directory-name-here>
   ```

   예를 들면 다음과 같습니다.

   ```sh
   mkdir C:\myhostdir
   ```
1. **바인딩을** 사용 하 여 디렉터리를 탑재 하 고 Azure Portal에서 Event Grid 모듈을 다시 배포 합니다.

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
    >Bind 값의 두 번째 부분을 변경 하지 마십시오. 모듈의 특정 위치를 가리킵니다. Windows의 Event Grid 모듈은 **C:\\app\\metadataDb**여야 합니다.

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
## <a name="persist-events"></a>이벤트 지속

이벤트 지 속성을 사용 하려면 먼저 위의 섹션을 사용 하 여 볼륨 탑재 또는 호스트 디렉터리 탑재를 통해 이벤트 지 속성을 사용 하도록 설정 해야 합니다.

이벤트 유지에 유의 해야 할 중요 한 사항은 다음과 같습니다.

* 유지 이벤트는 이벤트 구독 별로 사용 하도록 설정 되며 볼륨 또는 디렉터리가 탑재 되 면 옵트인 (opt in) 됩니다.
* 이벤트 지 속성은 생성 시 이벤트 구독에서 구성 되며, 이벤트 구독을 만든 후에는 수정할 수 없습니다. 이벤트 지 속성을 설정/해제 하려면 이벤트 구독을 삭제 하 고 다시 만들어야 합니다.
* 이벤트 지속은 메모리 작업 보다 거의 항상 느리지만 속도 차이는 드라이브의 특징에 따라 매우 다릅니다. 속도와 안정성 간의 균형은 모든 메시징 시스템에 고유 하지만 대규모에서 매우 두드러집니다.

이벤트 구독에서 이벤트 지 속성을 사용 하도록 설정 `persistencePolicy` 하려면 `true`를로 설정 합니다.

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