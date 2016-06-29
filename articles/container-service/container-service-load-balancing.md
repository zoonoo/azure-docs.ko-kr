<properties
   pageTitle="Azure 컨테이너 서비스 클러스터 부하 분산 | Microsoft Azure"
   description="Azure 컨테이너 서비스 클러스터를 부하 분산합니다."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="컨테이너, 마이크로 서비스, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Azure 컨테이너 서비스 클러스터 부하 분산

이 문서에서는 Azure Load Balancer 뒤에 서비스를 제공하도록 강화할 수 있는 웹 프런트 엔드를 설정합니다.

## 필수 조건

DC/OS Orchestrator 유형을 사용하여 [Azure 컨테이너 서비스의 인스턴스를 배포](container-service-deployment.md)하고 [클라이언트에서 클러스터에 연결할 수 있는지 확인합니다](container-service-connect.md). 또한 다음 단계를 수행합니다.
[AZURE.INCLUDE [DC/OS 명령줄 인터페이스(CLI) 설치](../../includes/container-service-install-dcos-cli-include.md)]


## 부하 분산

컨테이너 서비스 클러스터에는 두 개의 부하 분산 계층이 있습니다. 공용 진입점(최종 사용자가 도달)을 위한 Azure Load Balancer와 인바운드 요청을, 요청을 서비스하는 컨테이너 인스턴스로 라우팅하는 기본 Marathon Load Balancer(marathon-lb)입니다. 서비스를 제공하는 컨테이너를 확장함에 따라 marathon-lb는 동적으로 조정됩니다.

## Marathon Load Balancer

Marathon Load Balancer는 배포한 컨테이너를 기준으로 동적으로 자체 재구성합니다. 컨테이너 또는 에이전트 손실에 복원력이 있으며 손실이 발생하는 경우 Mesos는 다른 곳에서 컨테이너를 다시 시작하고 Marathon Load Balancer를 다시 구성합니다.

Marathon Load Balancer를 설치하려면 클라이언트 컴퓨터에서 다음 명령을 실행합니다.

```bash
dcos package install marathon-lb
```

이제 marathon-lb 패키지가 있으며 다음 구성을 사용하여 간단한 웹 서버를 배포할 수 있습니다.


```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "tutum/hello-world",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

이 작업의 주요 부분은 다음과 같습니다.
  * HAProxy\_0\_VHOST 값을 에이전트에 대한 부하 분산 장치의 FQDN으로 설정합니다. `<acsName>agents.<region>.cloudapp.azure.com` 양식입니다. 예를 들어 `West US` 지역에 `myacs` 이름으로 컨테이너 서비스 클러스터를 만든 경우 FQDN은 `myacsagents.westus.cloudapp.azure.com`이 됩니다. 또한 [Azure 포털](https://portal.azure.com)의 컨테이너 서비스에 대해 만든 리소스 그룹에서 리소스를 조사할 때 부하 분산 장치의 이름에 "에이전트"를 검색하여 찾을 수도 있습니다.
  * servicePort를 10,000 이상의 포트로 설정합니다. 이 컨테이너에서 실행되는 서비스를 식별하고 marathon-lb는 이를 사용하여 균형을 조정할 서비스를 식별합니다.
  * HAPROXY\_GROUP 레이블을 "external"로 설정합니다.
  * HostPort를 0으로 설정합니다. 이렇게 하면 해당 Marathon에서 사용 가능한 포트를 임의로 할당합니다.

이 JSON을 `hello-web.json`이라는 파일로 복사하고 컨테이너를 배포하는 데 사용합니다.

```bash
dcos marathon app add hello-web.json
```

## Azure Load Balancer

Azure Load Balancer는 기본적으로 포트 80, 8080 및 443을 노출합니다. 이러한 세 가지 포트 중 하나를 사용하는 경우(위의 예에서 실행하는 것처럼) 수행할 작업이 없습니다. 에이전트 부하 분산 장치의 FQDN에 도달할 수 있어야 하고 새로 고칠 때마다 라운드 로빈 방식으로 3개의 웹 서버 중 하나에 도달합니다. 하지만 다른 포트를 사용하는 경우 부하 분산 장치에서 사용한 포트에 대해 라운드 로빈 규칙 및 프로브를 추가해야 합니다. 이는 [Azure CLI](../xplat-cli-azure-resource-manager.md)에서 `azure lb rule create` 및 `azure lb probe create` 명령으로 수행할 수 있습니다.


## 추가 시나리오

다른 서비스를 노출하기 위해 다른 도메인을 사용하는 시나리오를 포함할 수 있습니다. 예:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

이를 달성하려면 도메인을 특정 marathon-lb 경로에 연결하는 방법을 제공하는 [가상 호스트](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)를 확인하세요.

또는 서로 다른 포트를 노출하고 marathon-lb 뒤의 올바른 서비스에 다시 매핑할 수 있습니다. 예:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## 다음 단계

Marathon Load Balancer에 대한 자세한 내용은 이 [부하 분산 블로그 게시물](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)을 확인하세요.

<!---HONumber=AcomDC_0622_2016-->