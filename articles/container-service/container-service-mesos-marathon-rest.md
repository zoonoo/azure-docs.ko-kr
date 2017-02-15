---
title: "REST API를 통해 Azure Container Service 컨테이너 관리 | Microsoft 문서"
description: "Marathon REST API를 사용하여 컨테이너를 Azure 컨테이너 서비스 Mesos 클러스터에 배포합니다."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 54832afbc9a7bf1d660de3fd898ad5c97715ca5d
ms.openlocfilehash: a01993eb01b9e05b4848d5a81b841fe10ccae035


---
# <a name="container-management-through-the-rest-api"></a>REST API를 통해 컨테이너 관리
DC/OS는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공합니다. DC/OS의 상단에 계산 워크로드의 예약 및 실행을 관리하는 프레임워크가 있습니다.

프레임워크는 수많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon을 사용하여 컨테이너 배포를 만들고 확장할 수 있는 방법을 설명합니다. 이러한 예제를 통해 작업하기 전에 Azure 컨테이너 서비스에 구성된 DC/OS 클러스터가 필요합니다. 또한 이 클러스터에 원격으로 연결해야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 컨테이너 서비스 클러스터 배포](container-service-deployment.md)
* [Azure 컨테이너 서비스 클러스터에 연결](container-service-connect.md)

Azure Container Service 클러스터에 연결한 후에 http://localhost:local-port를 통해 DC/OS 및 관련된 REST API에 액세스할 수 있습니다. 이 문서의 예제에서는 포트 80에서 터널링하는 것을 가정합니다. 예를 들어, Marathon 끝점은 있으신 `http://localhost/marathon/v2/`에 도달할 수 있습니다. 다양한 API에 대한 자세한 내용은 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 및 [Chronos API](https://mesos.github.io/chronos/docs/api.html)에 대한 Mesosphere 문서와 [Mesos 스케줄러 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)에 대한 Apache 문서를 참조하세요.

## <a name="gather-information-from-dcos-and-marathon"></a>DC/OS 및 Marathon에서 정보 수집
DC/OS 클러스터에 컨테이너를 배포하기 전에 이름, DC/OS 에이전트의 현재 상태와 같은 DC/OS 클러스터에 대한 정보를 수집합니다. 이렇게 하려면 DC/OS REST API에서 `master/slaves` 끝점을 쿼리합니다. 모든 작업이 제대로 진행되었다면 쿼리는 DC/OS 에이전트와 각각에 대한 여러 속성 목록을 반환합니다.

```bash
curl http://localhost/mesos/master/slaves
```

이제는 Marathon `/apps` 끝점을 사용하여 DC/OS 클러스터에 대한 현재 응용 프로그램 배포를 확인합니다. 새 클러스터인 경우에 앱에 대한 빈 배열이 표시됩니다.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker로 포맷된 컨테이너 배포
Marathon을 통해 원하는 배포를 설명하는 JSON 파일을 사용하여 Docker로 포맷된 컨테이너를 배포합니다. 다음 샘플은 Nginx 컨테이너를 배포하며 DC/OS 에이전트의 포트 80을 컨테이너의 포트 80에 바인딩합니다. ‘acceptedResourceRoles’ 속성은 ‘slave_public’으로 설정됩니다. 이렇게 하면 컨테이너가 공용 에이전트 규모 집합의 에이전트에 배포됩니다.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker로 포맷된 컨테이너를 배포하기 위해 사용자 고유의 JSON 파일을 만들거나 [Azure 컨테이너 서비스 데모](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)에 제공된 샘플을 사용합니다. 액세스할 수 있는 위치에 저장합니다. 다음으로 컨테이너를 배포하려면 다음 명령을 실행합니다. JSON 파일의 이름을 지정합니다.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

다음과 유사하게 출력됩니다.

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

이제 응용 프로그램에 대해 Marathon을 쿼리하면 이 새 응용 프로그램이 출력에 나타납니다.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>컨테이너 확장
Marathon API를 사용하여 응용 프로그램 배포의 규모를 확장 또는 감축할 수도 있습니다. 앞의 예제에서 응용 프로그램의 인스턴스를 하나 배포했습니다. 응용 프로그램의 세 인스턴스를 확장해 보겠습니다. 이렇게 하려면 다음 JSON 텍스트를 사용하여 JSON 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

응용 프로그램의 규모를 확장하려면 다음 명령을 실행합니다.

> [!NOTE]
> URI은 http://localhost/marathon/v2/apps/이고 그 다음에 크기를 조정할 응용 프로그램의 ID가 표시됩니다. 여기에 제공된 Nginx 샘플을 사용하는 경우 URI는 http://localhost/marathon/v2/apps/nginx입니다.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

마지막으로, 응용 프로그램에 대해 Marathon 끝점을 쿼리합니다. 이제 세 가지 Nginx 컨테이너가 표시됩니다.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>다음 연습에 대해 PowerShell을 사용합니다. PowerShell과 Marathon REST API 상호 작용
Windows 시스템에서 PowerShell 명령을 사용하여 이러한 동일한 작업을 수행할 수 있습니다.

에이전트 이름 및 에이전트 상태와 같은 DC/OS 클러스터에 대한 정보를 수집 하려면 다음 명령을 실행합니다.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Marathon을 통해 원하는 배포를 설명하는 JSON 파일을 사용하여 Docker로 포맷된 컨테이너를 배포합니다. 다음 샘플은 Nginx 컨테이너를 배포하며 DC/OS 에이전트의 포트 80을 컨테이너의 포트 80에 바인딩합니다.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

사용자 고유의 JSON 파일을 만들거나 [Azure 컨테이너 서비스 데모](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)에 제공된 샘플을 사용합니다. 액세스할 수 있는 위치에 저장합니다. 다음으로 컨테이너를 배포하려면 다음 명령을 실행합니다. JSON 파일의 이름을 지정합니다.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API를 사용하여 응용 프로그램 배포의 규모를 확장 또는 감축할 수도 있습니다. 앞의 예제에서 응용 프로그램의 인스턴스를 하나 배포했습니다. 응용 프로그램의 세 인스턴스를 확장해 보겠습니다. 이렇게 하려면 다음 JSON 텍스트를 사용하여 JSON 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

응용 프로그램의 규모를 확장하려면 다음 명령을 실행합니다.

> [!NOTE]
> URI은 http://localhost/marathon/v2/apps/이고 그 다음에 크기를 조정할 응용 프로그램의 ID가 표시됩니다. 여기에 제공된 Nginx 샘플을 사용하는 경우 URI는 http://localhost/marathon/v2/apps/nginx입니다.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>다음 단계
* [Mesos HTTP 끝점에 대해 자세히 알아봅니다](http://mesos.apache.org/documentation/latest/endpoints/).
* [Marathon REST API에 대해 자세히 알아봅니다](https://mesosphere.github.io/marathon/docs/rest-api.html).




<!--HONumber=Dec16_HO2-->


