<properties
   pageTitle="Azure 컨테이너 서비스 클러스터에 연결 | Microsoft Azure"
   description="SSH 터널을 사용하여 Azure 컨테이너 서비스 클러스터에 연결합니다."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
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
   ms.author="rogardle"/>
   

# Azure 컨테이너 서비스 클러스터에 연결

Azure 컨테이너 서비스에 의해 배포된 Mesos 및 Swarm 클러스터는 REST 끝점을 노출하지만 이러한 끝점은 외부 세계에 열려 있지 않습니다. 이러한 끝점을 관리하기 위해 SSH 터널을 만들어야 합니다. SSH 터널이 설정되면 클러스터 끝점에 대해 명령을 실행하고 사용자 자신의 시스템에 있는 브라우저를 통해 클러스터 UI를 볼 수 있습니다. 이 문서에서는 Linux, OSX 및 Windows에서 SSH 터널을 만드는 방법을 안내합니다.

> 클러스터 관리 시스템으로 SSH 세션을 만들 수는 있지만 권장되지 않습니다. 관리 시스템에 대해 직접 작업하면 의도하지 않은 구성 변경에 대한 위험에 노출됩니다.

## Linux/OSX에서 SSH 터널

먼저 부하 분산된 마스터의 공용 DNS 이름을 찾습니다. 이렇게 하려면 리소스 그룹을 확장하여 각 리소스가 표시되도록 합니다. 마스터의 공용 IP 주소를 찾아 선택합니다. 그러면 DNS 이름이 포함된 공용 IP 주소에 대한 정보가 있는 블레이드가 열립니다. 이 이름은 나중에 사용되므로 저장합니다. <br />

![Putty 연결](media/pubdns.png)

이제, 셸을 열고 다음 명령을 실행합니다. 여기서,

**PORT**는 노출하려는 끝점의 포트입니다. Swarm의 경우 2375이고, Mesos는 포트 80을 사용합니다. **USERNAME**은 클러스터를 배포할 때 제공한 사용자 이름입니다. **DNSPREFIX**은 클러스터를 배포할 때 제공한 DNS 접두사입니다. **REGION**은 리소스 그룹이 있는 하위 지역입니다.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos 터널

Mesos 관련 끝점에 대한 터널을 열려면 다음과 비슷한 명령을 실행합니다.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

이제 Mesos 관련 끝점은 다음을 통해 액세스할 수 있습니다.

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`. 

마찬가지로, 각 응용 프로그램에 대한 rest API는 이 터널을 통해 연결할 수 있습니다. Marathon - `http://localhost/marathon/v2`. 사용할 수 있는 다양한 API에 대한 자세한 내용은 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 및 [Chronos API](https://mesos.github.io/chronos/docs/api.html)에 대한 Mesosphere 문서와 [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)에 대한 Apache 문서를 참조하세요.

### Swarm 터널

Swarm 끝점에 대한 터널을 열려면 다음 명령과 비슷한 명령을 실행합니다.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

이제 다음과 같이 DOCKER\_HOST 환경 변수에 설정하고 정상적으로 Docker CLI를 계속 사용할 수 있습니다.

```
export DOCKER_HOST=:2375
```

## Windows에서 SSH 터널

Windows에서 SSH 터널을 만드는 데 여러 옵션을 사용할 수 있으며 이 문서에서는 Putty 사용에 대해 자세히 설명합니다.

Windows 시스템으로 Putty를 다운로드하고 응용 프로그램을 실행합니다.

클러스터 관리 사용자 이름 및 클러스터에서 첫 번째 마스터의 공용 DNS 이름으로 구성된 호스트 이름을 입력합니다. 호스트 이름은 다음과 같이 표시됩니다. `adminuser@PublicDNS` 포트에 2200를 입력합니다.

![Putty 연결](media/putty1.png)

`SSH` 및 `Authentication`을 선택하고 인증을 위한 개인 키 파일을 추가합니다.

![Putty 연결](media/putty2.png)

`Tunnels`을 선택하고 다음 전달된 포트를 `configure`합니다.
- **원본 포트:** 기본 설정(Mesos의 경우 80 및 Swarm의 경우 2375)
- **대상:** localhost:80(Mesos) 또는 localhost:2375(Swarm)

다음 예제에서는 Mesos에 대해 구성되었지만 Docker Swarm에 대한 구성도 이와 유사합니다.

> 이 터널을 만들 때 포트 80은 사용 중이 아니어야 합니다.

![Putty 연결](media/putty3.png)

완료되면 연결 구성을 저장하고 putty 세션에 연결합니다. 연결되면 포트 구성을 Putty 이벤트 로그에서 볼 수 있습니다.

![Putty 연결](media/putty4.png)

Mesos에 대한 터널이 구성되면 관련 끝점을 다음으로 액세스할 수 있습니다.

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`. 

Docker Swarm에 대한 터널이 구성되면 Docker CLI를 통해 Swarm 클러스터에 액세스할 수 있습니다. 먼저 ` :2375` 값과 함께 `DOCKER_HOST`로 명명된 Windows 환경 변수를 구성해야 합니다.

## 다음 단계
 
Mesos 또는 Swarm으로 컨테이너를 배포 및 관리합니다.
 
- [ACS 및 Mesos 작업](./container-service-mesos-marathon-rest.md)

<!----HONumber=AcomDC_0309_2016-->