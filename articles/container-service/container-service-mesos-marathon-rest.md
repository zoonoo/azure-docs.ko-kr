<properties
   pageTitle="REST API로 ACS 컨테이너 관리 | Microsoft Azure"
   description="Marathon REST API를 사용하여 컨테이너를 Azure 컨테이너 서비스 클러스터 서비스에 배포합니다."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# REST API로 컨테이너 관리

Mesos는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공 합니다. Mesos의 상단에서 프레임워크가 계산 워크로드의 예약 및 실행을 관리합니다. 프레임워크는 수많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon으로 컨테이너 배포를 만들고 확장하는 방법에 대해 자세히 설명합니다. 이러한 예제를 통해 작업하기 전에 ACS에 Mesos 클러스터가 구성되어야 하고 이 클러스터에 대한 원격 연결이 있어야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 컨테이너 서비스 클러스터 배포](./container-service-deployment.md) 
- [ACS 클러스터에 연결](./container-service-connect.md)


SSH 터널이 설정되었으면 `http://localhost:LOCAL_PORT`를 통해 Mesos 관련 rest API에 액세스할 수 있습니다. 아래 예제에서는 포트 80에서 터널링 중이라고 가정합니다. 예를 들어 `http://localhost/marathon/v2`가 Marathon API에 대한 끝점이 됩니다. 사용할 수 있는 다양한 API에 대한 자세한 내용은 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 및 [Chronos API](https://mesos.github.io/chronos/docs/api.html)에 대한 Mesosphere 문서와 [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)에 대한 Apache 문서를 참조하세요.

## Mesos 및 Marathon에서 정보 수집

Mesos 클러스터에 컨테이너를 배포하기 전에 이름, Mesos 에이전트의 현재 상태와 같은 Mesos 클러스터에 대한 정보를 수집합니다. 이렇게 하려면 Mesos 마스터에서 `master/slaves` 끝점을 쿼리합니다. 모든 작업이 제대로 진행되었다면 Mesos 에이전트와 각각에 대한 여러 속성 목록이 표시됩니다.

```bash
curl http://localhost/master/slaves
```

이제는 Marathon `/apps` 끝점을 사용하여 Mesos 클러스터에 대한 현재 Marathon 배포를 확인합니다. 새 클러스터인 경우에 앱에 대한 빈 배열이 표시됩니다.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Docker 컨테이너 배포

Docker 컨테이너는 원하는 배포를 설명하는 json 파일을 사용하여 Marathon을 통해 배포됩니다. 다음 샘플은 nginx 컨테이너를 배포하며 Mesos 에이전트의 포트 80을 컨테이너의 포트 80에 바인딩합니다.

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

Docker 컨테이너를 배포하려면 사용자 고유의 json 파일을 만들거나 여기 [Azure ACS 데모](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)에 제공된 샘플을 사용하고 액세스 가능한 위치에 저장합니다. 다음으로, 다음 명령을 실행하며 컨테이너를 배포할 json 파일의 이름을 지정합니다.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

다음과 유사하게 출력됩니다.

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

이제 실행 중인 응용 프로그램에 대해 Marathon을 쿼리하면 이 새 응용 프로그램이 출력에 표시됩니다..

```
curl localhost/marathon/v2/apps
```

## Docker 컨테이너 확장

응용 프로그램 배포를 외부 또는 내부로 확장하는 데 Marathon API를 사용할 수도 있습니다. 이전 예제에서는 응용 프로그램의 한 인스턴스를 배포했으며 이를 세 개의 인스턴스로 규모 확장해 보겠습니다. 이렇게 하려면 다음 json 텍스트로 json 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

응용 프로그램을 확장하려면 다음 명령을 실행합니다.

> 참고 - URI는 http://localhost/marathon/v2/apps/이며 그 다음 확장할 응용 프로그램의 ID입니다. 여기에 제공된 nginx 샘플을 사용하는 경우 URI는 http://localhost/v2/nginx입니다.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

마지막으로, 응용 프로그램 인스턴스에 대해 Marathon 끝점을 쿼리합니다. 이제 세 개가 있음을 알 수 있습니다.

```
curl localhost/marathon/v2/apps
```

## Marathon REST API PowerShell

Windows 시스템에서 PowerShell을 사용하여 이와 동일한 작업을 수행할 수 있습니다. 이 빠른 연습은 마지막 연습과 유사한 태스크를 완료하며 이때 PowerShell 명령을 사용합니다.

에이전트 이름 및 에이전트 상태와 같은 Mesos 클러스터에 대한 정보를 수집 하려면 다음 명령을 실행합니다.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Docker 컨테이너는 원하는 배포를 설명하는 json 파일을 사용하여 Marathon을 통해 배포됩니다. 다음 샘플은 nginx 컨테이너를 배포하며 Mesos 에이전트의 포트 80을 컨테이너의 포트 80에 바인딩합니다.

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

사용자 고유의 json 파일을 만들거나 여기 [Azure ACS 데모](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)에 제공된 샘플을 사용하고 액세스 가능한 위치에 저장합니다. 다음으로, 다음 명령을 실행하며 컨테이너를 배포할 json 파일의 이름을 지정합니다.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

응용 프로그램 배포를 외부 또는 내부로 확장하는 데 Marathon API를 사용할 수도 있습니다. 이전 예제에서는 응용 프로그램의 한 인스턴스를 배포했으며 이를 세 개의 인스턴스로 규모 확장해 보겠습니다. 이렇게 하려면 다음 json 텍스트로 json 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

응용 프로그램을 확장하려면 다음 명령을 실행합니다.

> 참고 - URI는 http://loclahost/marathon/v2/apps/이며 그 다음 확장할 응용 프로그램의 ID입니다. 여기에 제공된 nginx 샘플을 사용하는 경우 URI는 http://localhost/v2/nginx입니다.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0218_2016-->