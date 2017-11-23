---
title: "Azure Container Instances 문제 해결"
description: "Azure Container Instances 관련 문제 해결 방법 알아보기"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 78bd45f7f71fd25e351d4e9b922a6a3f171437fd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure Container Instances로의 배포 문제 해결

이 문서에는 컨테이너를 Azure Container Instances로 배포하는 경우 문제를 해결하는 방법을 보여줄 뿐만 아니라, 발생할 수 있는 일반적인 문제 중 일부를 설명합니다.

## <a name="get-diagnostic-events"></a>진단 이벤트 가져오기

컨테이너 내에서 응용 프로그램 코드에서 로그를 보려면 [az 컨테이너 로그](/cli/azure/container#logs) 명령을 사용할 수 있습니다. 하지만 컨테이너가 성공적으로 배포되지 않을 경우 Azure Container Instances 리소스 공급자가 제공하는 진단 정보를 검토해야 합니다. 컨테이너에 대한 이벤트를 보려면 다음 명령을 실행하십시오.

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

출력에 배포 이벤트와 함께 컨테이너의 핵심 속성이 포함되어 있습니다.

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>일반 배포 문제

배포에서 대부분 오류를 차지하는 몇 가지 일반적인 문제가 있습니다.

## <a name="unable-to-pull-image"></a>이미지를 풀링할 수 없음

Azure Container Instances가 초기에 이미지를 풀링할 수 없는 경우 결과적으로 실패하기 전까지 당분간 재시도합니다. 이미지를 풀링할 수 없는 경우에는 다음과 같은 이미지가 표시됩니다

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

해결하려면 해당 컨테이너를 삭제하고 이미지 이름을 정확하게 입력했는 세심하게 주의하면서 배포를 다시 시도하세요.

## <a name="container-continually-exits-and-restarts"></a>컨테이너가 지속적으로 종료 후 다시 시작함

컨테이너가 완료되었다가 자동으로 다시 시작되면 [다시 시작 정책](container-instances-restart-policy.md)을 **OnFailure** 또는 **Never**로 설정해야 합니다. **OnFailure**를 지정해도 컨테이너가 계속 다시 시작되면 컨테이너에서 실행된 응용 프로그램이나 스크립트에 문제가 있을 수 있습니다.

컨테이너 인스턴스 API는 `restartCount` 속성을 포함합니다. 컨테이너에 대한 다시 시작 횟수를 확인하기 위해 Azure CLI 2.0의 [az container show](/cli/azure/container#az_container_show) 명령을 사용할 수 있습니다. 다음 예제 출력(간단히 하기 위해 일부 자름)에서는 출력 마지막에 `restartCount` 속성이 있습니다.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 배포판에 대한 대부분 컨테이너 이미지는 기본 명령으로 Bash와 같은 셸을 설정합니다. 자체 셸은 장기 실행 서비스이므로, 기본값 **Always** 다시 시작 정책이 구성되었을 때 이러한 컨테이너는 즉시 종료된 후 다시 시작 루프를 시작합니다.

## <a name="container-takes-a-long-time-to-start"></a>컨테이너는 시작하는 데 오래 걸림

컨테이너가 시작하는 데 오래 걸리지만, 결과적으로 성공할 경우 먼저 컨테이너 이미지의 크기를 살펴 보세요. Azure Container Instances가 요청 시 컨테이너 이미지를 풀링하므로, 경험하는 시작 시간은 크기와 직접 관련이 있습니다.

Docker CLI를 사용하여 컨테이너 이미지의 크기를 볼 수 있습니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

이미지 크기를 작게 유지하는 핵심 요소는 최종 이미지에 런타임 시 필요하지 않은 것은 아무 것도 포함하지 않는 것입니다. 이 작업을 수행하는 한 가지 방법은 [다단계 빌드](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)를 활용하는 것입니다. 다단계 빌드를 통해 간편하게 최종 이미지에 빌드 시간에 필요한 추가 콘텐츠가 아닌 응용 프로그램에 필요한 아티팩트만 포함하도록 할 수 있습니다.

컨테이너 시작 시간에서 이미지 풀의 영향을 줄이는 다른 방법은 Azure Container Instances를 사용하는 같은 영역에서 Azure Container Registry를 사용하여 컨테이너 이미지를 호스팅하는 것입니다. 이를 통해 컨테이너 이미지가 이동해야 하는 네트워크 경를 단축하여 다운로드 시간을 크게 줄여 줍니다.

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류

Azure에서 다양한 지역별 리소스 부하로 인해 컨테이너 인스턴스 배포를 시도할 때 다음 오류가 발생할 수 있습니다.

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

이 오류는 배포하려는 지역에서 과부하로 인해 컨테이너에 대해 지정된 리소스를 해당 시간에 할당할 수 없음을 나타냅니다. 다음 위험 완화 단계 중 하나 이상을 사용하여 문제를 해결합니다.

* [Azure Container Instances에 대한 지역 가용성](container-instances-region-availability.md)에 정의된 매개 변수 내에 컨테이너 배포 설정이 속하는지 확인합니다.
* 컨테이너에 대해 낮은 CPU 및 메모리 설정 지정
* 다른 Azure 지역에 배포
* 나중에 배포
