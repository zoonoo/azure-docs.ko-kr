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
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Azure 컨테이너 서비스 클러스터에 연결

Azure 컨테이너 서비스에 의해 배포되는 DC/OS 및 Swarm 클러스터는 REST 끝점을 노출합니다. 그러나 이러한 끝점 외부에 열려 있지 않습니다. 이러한 끝점을 관리하기 위해 SSH(보안 셸) 터널을 만들어야 합니다. SSH 터널이 설정되면 클러스터 끝점에 대해 명령을 실행하고 사용자 자신의 시스템에 있는 브라우저를 통해 클러스터 UI를 볼 수 있습니다. 이 문서에서는 Linux, OSX 및 Windows에서 SSH 터널을 만드는 방법을 안내합니다.

>[AZURE.NOTE] 클러스터 관리 시스템으로 SSH 세션을 만들 수 있습니다. 그러나 권장하지 않습니다. 관리 시스템에서 직접 작업하면 의도하지 않은 구성 변경에 대한 위험에 노출됩니다.

## Linux 또는 OS X에서 SSH 터널 만들기

Linux 또는 OS X에서 SSH 터널을 만들 때 먼저 수행할 작업은 부하 분산된 마스터의 공용 DNS 이름을 찾는 것입니다. 이렇게 하려면 리소스 그룹을 확장하여 각 리소스가 표시되도록 합니다. 마스터의 공용 IP 주소를 찾아 선택합니다. 그러면 DNS 이름이 포함된 공용 IP 주소에 대한 정보가 있는 블레이드가 열립니다. 이 이름은 나중에 사용되므로 저장합니다. <br />


![공용 DNS 이름](media/pubdns.png)

이제, 셸을 열고 다음 명령을 실행합니다. 여기서,

**PORT**는 노출하려는 끝점의 포트입니다. Swarm의 경우 2375입니다. DC/OS의 경우 포트 80을 사용합니다. **USERNAME**은 클러스터를 배포할 때 제공된 사용자 이름입니다. **DNSPREFIX**는 클러스터를 배포할 때 제공한 DNS 접두사입니다. **REGION**은 리소스 그룹이 있는 하위 지역입니다.

```bash
# ssh sample
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```

## DC/OS 터널

DC/OS 관련 끝점에 대한 터널을 열려면 다음과 비슷한 명령을 실행합니다.

```bash
# ssh sample

ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

이제 다음에서 DC/OS 관련 끝점에 액세스할 수 있습니다.

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

마찬가지로, 각 응용 프로그램에 대한 rest API는 이 터널을 통해 도달할 수 있습니다.

## Swarm 터널

Swarm 끝점에 대한 터널을 열려면 다음과 비슷한 명령을 실행합니다.

```bash
# ssh sample

ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

이제 다음과 같이 DOCKER\_HOST 환경 변수에 설정하고 정상적으로 Docker CLI(명령줄 인터페이스)를 계속 사용할 수 있습니다.

```bash
export DOCKER_HOST=:2375
```

## Windows에서 SSH 터널 만들기

Windows에서 SSH 터널을 만드는 방법은 여러 가지가 있습니다. 이 문서에서는 PuTTY를 사용하여 이 작업을 수행하는 방법을 설명합니다.

Windows 시스템으로 PuTTY를 다운로드하고 응용 프로그램을 실행합니다.

클러스터 관리 사용자 이름 및 클러스터에서 첫 번째 마스터의 공용 DNS 이름으로 구성된 호스트 이름을 입력합니다. **호스트 이름**은 다음과 같이 표시됩니다. `adminuser@PublicDNS`. **포트**에 2200을 입력합니다.

![PuTTY 구성 1](media/putty1.png)

`SSH` 및 `Authentication`를 선택합니다. 인증을 위한 개인 키 파일을 추가합니다.

![PuTTY 구성 2](media/putty2.png)

`Tunnels`을 선택하고 다음 전달된 포트를 구성합니다.
- **원본 포트:** 기본 설정은 DC/OS의 경우 80 또는 Swarm의 경우 2375를 사용합니다.
- **대상:** localhost:80(DC/OS) 또는 localhost:2375(Swarm)를 사용합니다.

다음 예제에서는 DC/OS에 대해 구성되었지만 Docker Swarm에 대한 구성도 이와 유사합니다.

>[AZURE.NOTE] 이 터널을 만들 때 포트 80은 사용 중이 아니어야 합니다.

![PuTTY 구성 3](media/putty3.png)

완료하면 연결 구성을 저장하고 PuTTY 세션에 연결합니다. 연결하면 PuTTY 이벤트 로그에서 포트 구성을 볼 수 있습니다.

![PuTTY 이벤트 로그](media/putty4.png)

DC/OS에 터널을 구성한 경우 다음에서 관련된 끝점에 액세스할 수 있습니다.

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Docker Swarm에 터널을 구성한 경우 Docker CLI를 통해 Swarm 클러스터에 액세스할 수 있습니다. 먼저 ` :2375` 값과 함께 `DOCKER_HOST`로 명명된 Windows 환경 변수를 구성해야 합니다.

## 다음 단계

DC/OS 또는 Swarm으로 컨테이너를 배포 및 관리합니다.

[Azure 컨테이너 서비스 및 DC/OS로 작업](./container-service-mesos-marathon-rest.md) [Azure 컨테이너 서비스 및 Docker Swarm으로 작업](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0518_2016-->