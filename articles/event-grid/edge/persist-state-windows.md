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
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992263"
---
# <a name="persist-state-in-windows"></a>Windows에서 상태 유지

Event Grid 모듈에서 만든 토픽 및 구독은 기본적으로 컨테이너 파일 시스템에 저장 됩니다. 지 속성을 사용 하지 않으면 모듈을 다시 배포 하면 생성 된 모든 메타 데이터가 손실 됩니다. 배포 간에 데이터를 유지 하려면 컨테이너 파일 시스템 외부에서 데이터를 유지 해야 합니다. 현재 메타 데이터만 유지 됩니다. 이벤트는 메모리에 저장 됩니다. 모듈을 다시 배포 하거나 다시 시작 하는 Event Grid 경우에는 배달 되지 않은 이벤트가 모두 손실 됩니다.

이 문서에서는 Windows 배포에서 지 속성을 사용 하 여 Event Grid 모듈을 배포 하는 데 필요한 단계를 제공 합니다.

> [!NOTE]
>Event Grid 모듈은 Windows에서 권한이 낮은 사용자 **ContainerUser** 실행 됩니다.

## <a name="persistence-via-volume-mount"></a>볼륨 탑재를 통한 지 속성

[Docker 볼륨](https://docs.docker.com/storage/volumes/) 은 배포 간에 데이터를 유지 하는 데 사용 됩니다. 볼륨을 탑재 하려면 docker 명령을 사용 하 여 볼륨을 만들고, 컨테이너에서이를 읽고 쓰고, 모듈을 배포할 수 있도록 권한을 부여 해야 합니다. Windows에서 필요한 권한이 있는 볼륨을 자동으로 만드는 프로 비전은 없습니다. 배포 하기 전에 만들어야 합니다.

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
    1. **보안**을 선택합니다.
    1. \* 그룹 또는 사용자 이름 아래에서 **편집**을 선택 합니다.
    1. **추가**를 선택 하 고 `Users`를 입력 한 다음 **이름 확인**을 선택 하 고 **확인**을 선택 합니다.
    1. *사용자의 사용 권한*아래에서 **수정**을 선택 하 고 **확인**을 선택 합니다.
1. **바인딩을** 사용 하 여이 볼륨을 탑재 하 고 Azure Portal에서 Event Grid 모듈을 다시 배포 합니다.

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
   >Bind 값의 두 번째 부분을 변경 하지 마십시오. 모듈의 특정 위치를 가리킵니다. Windows의 Event Grid 모듈의 경우 **C:\\app\\metadataDb**여야 합니다.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

또는 호스트 시스템에 디렉터리를 만들고 해당 디렉터리를 탑재 하도록 선택할 수 있습니다.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
