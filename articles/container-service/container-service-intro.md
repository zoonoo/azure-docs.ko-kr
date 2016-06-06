<properties
   pageTitle="Azure 컨테이너 서비스 소개 | Microsoft Azure"
   description="Azure 컨테이너 서비스는 컨테이너화된 응용 프로그램을 실행하는 미리 구성된 가상 컴퓨터의 클러스터를 간단히 만들고 구성하고 관리하는 방법을 제공합니다."
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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Azure 컨테이너 서비스 소개

Azure 컨테이너 서비스를 사용하면 컨테이너화된 응용 프로그램을 실행하는 미리 구성된 가상 컴퓨터의 클러스터를 보다 간편하게 만들고 구성하고 관리할 수 있습니다. Azure 컨테이너 서비스는 일반적인 오픈 소스 예약 및 오케스트레이션 도구의 최적화된 구성을 사용합니다. 이를 통해 기존 기술을 사용하거나 크고 확장된 커뮤니티 전문 지식의 본문을 이용하여 Microsoft Azure의 컨테이너 기반 응용 프로그램을 배포하고 관리할 수 있습니다.

<br /> ![Azure 컨테이너 서비스는 Azure의 여러 호스트에서 컨테이너화된 응용 프로그램을 관리하는 방법을 제공합니다.](./media/acs-intro/acs-cluster.png) <br /><br />

Azure 컨테이너 서비스는 Docker 컨테이너 형식을 활용하여 응용 프로그램 컨테이너가 완전히 이식 가능한지 확인합니다. 또한 이러한 응용 프로그램을 수천 내지 수만 개의 컨테이너로 확장할 수 있도록 선택한 Marathon 및 DC/OS 또는 Docker Swarm을 지원합니다.

Azure 컨테이너 서비스를 사용하면 오케스트레이션 계층의 이식성을 포함하여 응용 프로그램 이식성을 유지하면서 Azure의 엔터프라이즈급 기능을 활용할 수 있습니다.

Azure 컨테이너 서비스 사용
-----------------------------

Azure 컨테이너 서비스를 사용하는 우리의 목표는 현재 우리 고객들 사이에서 인기 있는 오픈 소스 도구 및 기술을 사용하여 컨테이너 호스팅 환경을 제공하는 것입니다. 이를 위해 사용자가 선택한 Orchestrator에 대한 표준 API 끝점을 노출합니다. 이러한 끝점을 사용하면 해당 끝점과 통신할 수 있는 모든 소프트웨어를 활용할 수 있습니다. 예를 들어 Docker Swarm 끝점의 경우 Docker CLI(명령줄 인터페이스)를 사용하도록 선택할 수 있고 DC/OS의 경우 DCOS CLI를 사용하도록 선택할 수 있습니다.

Azure 컨테이너 서비스를 사용하여 Docker 클러스터 만들기
-------------------------------------------------------

Azure 컨테이너 서비스를 사용하기 시작하려면 Azure Resource Manager 템플릿을 사용하여 Azure 컨테이너 서비스 클러스터를 배포합니다. DC/OS 또는 Docker Swarm을 사용하여 이 배포를 다양한 크기 및 가용성 옵션으로 구성할 수 있습니다. Azure Resource Manager 템플릿은 Azure CLI 또는 PowerShell을 사용하여 Azure 포털을 통해 배포할 수 있습니다. 또한 추가 또는 고급 Azure 구성을 포함하도록 템플릿을 수정할 수 있습니다. Azure 컨테이너 서비스 클러스터 배포에 대한 자세한 내용은 [Azure 컨테이너 서비스 클러스터 배포](container-service-deployment.md)를 참조하세요.

응용 프로그램 배포
------------------------

Azure 컨테이너 서비스는 오케스트레이션을 위해 Docker Swarm 또는 DC/OS 옵션을 제공합니다.

### DC/OS 사용

DC/OS는 Apache Software Foundation에 상주하는 오픈 소스 프로젝트입니다. 사용자 및 참가자로 [IT 업계에서 가장 유명한 기업](http://mesos.apache.org/documentation/latest/powered-by-mesos/)의 일부를 나열합니다.

![에이전트 및 마스터가 표시된 Swarm에 대해 구성된 Azure 컨테이너 서비스](media/acs-intro/dcos.png)

DC/OS에는 매우 유용한 기능 집합이 포함되어 있습니다.

-   노드 10,000개로의 확장성

-   Apache ZooKeeper를 사용하는 내결함성 있는 복제된 마스터 및 슬레이브

-   Docker 형식의 컨테이너에 대한 지원

-   Linux 컨테이너를 사용하여 작업 간에 네이티브 격리

-   다중 리소스 예약(메모리, CPU, 디스크 및 포트)

-   새로운 병렬 응용 프로그램을 개발하기 위한 Java, Python 및 C++ API

-   클러스터 상태를 볼 수 있는 웹 UI

DC/OS는 Azure 컨테이너 서비스에서 워크로드를 예약하는 데 사용할 수 있는 다수의 [프레임워크](http://mesos.apache.org/documentation/latest/frameworks/)를 지원합니다. 기본적으로 Azure 컨테이너 서비스에는 Marathon 및 Chronos 프레임워크가 포함되어 있습니다.

#### Marathon 및 Chronos 사용

Marathon은 cgroups의 서비스 또는 Docker 형식의 컨테이너(Azure 컨테이너 서비스의 경우)에 대한 클러스터 전체 초기화 및 제어 시스템입니다. 이는 종속성 및 시간 기반 일정을 처리하는 DC/OS에 대한 내결함성 있는 작업 스케줄러인 Chronos와 이상적인 파트너입니다.

Marathon 및 Chronos는 응용 프로그램을 배포할 수 있는 웹 UI를 제공합니다. 사용자는 배포 시 DNS\_PREFIX 및 REGION이 모두 정의된 `http://DNS\_PREFIX.REGION.cloudapp.azure.com`과 유사한 URL에서 웹 UI에 액세스할 수 있습니다. 물론, 사용자는 자체 DNS 이름을 제공할 수도 있습니다. Marathon 웹 UI를 사용하여 컨테이너를 실행하는 방법에 대한 자세한 내용은 [웹 UI를 통한 컨테이너 관리](container-service-mesos-marathon-ui.md)를 참조하세요.

또한 Marathon 및 Chronos와 통신하기 위해 REST API를 사용할 수 있습니다. 각 도구에 사용할 수 있는 여러 클라이언트 라이브러리가 있습니다. 이러한 라이브러리는 다양한 언어를 지원할 뿐만 아닐라 모든 언어로 된 HTTP 프로토콜을 사용할 수 있습니다. 또한 인기 있는 다양한 DevOps 도구는 이러한 스케줄러에 대한 지원을 제공합니다. Azure 컨테이너 서비스 클러스터를 사용하여 작업하는 경우 운영 팀에 최대의 유연성을 제공합니다. Marathon REST API를 사용하여 컨테이너를 실행하는 방법에 대한 자세한 내용은 [REST API로 컨테이너 관리](container-service-mesos-marathon-rest.md)를 참조하세요.

### Docker Swarm 사용

Docker Swarm은 Docker에 대한 네이티브 클러스터링을 제공합니다. Docker Swarm은 표준 Docker API를 준수하므로 이미 Docker 데몬과 통신하는 모든 도구에서 Swarm을 사용하여 Azure 컨테이너 서비스의 여러 호스트에 대해 투명하게 크기를 조정할 수 있습니다.

![jumpbox, 에이전트 및 마스터가 표시된 DC/OS를 사용하도록 구성된 Azure 컨테이너 서비스](media/acs-intro/acs-swarm2.png)

Swarm 클러스터의 컨테이너를 관리하기 위해 지원된 도구를 포함하지만 다음으로 제한되지 않습니다.

-   Dokku

-   Docker CLI 및 Docker Compose

-   Krane

-   Jenkins

비디오
------
AzureCon 알림:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Azure 컨테이너 서비스 시작:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0525_2016-->