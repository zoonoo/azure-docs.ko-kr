<properties
   pageTitle="웹 UI를 통해 Azure 컨테이너 서비스 컨테이너 관리 | Microsoft Azure"
   description="Marathon 웹 UI를 사용하여 컨테이너를 Azure 컨테이너 서비스 클러스터 서비스에 배포합니다."
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

# 웹 UI 통해 컨테이너 관리

DC/OS는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공합니다. DC/OS의 상단에 계산 워크로드의 예약 및 실행을 관리하는 프레임워크가 있습니다.

프레임워크는 수많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon으로 컨테이너 배포를 만들고 확장할 수 있는 방법을 설명합니다. 이러한 예제를 통해 작업하기 전에 Azure 컨테이너 서비스에 구성된 DC/OS 클러스터가 필요합니다. 또한 이 클러스터에 원격으로 연결해야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 컨테이너 서비스 클러스터 배포](container-service-deployment.md)
- [Azure 컨테이너 서비스 클러스터에 연결](container-service-connect.md)

## DC/OS UI 탐색

SSH(Secure Shell) 터널이 설정된 상태에서 http://localhost/로 이동합니다. DC/OS 웹 UI가 로드되면서 클러스터에 대한 정보(예: 사용된 리소스, 활성 에이전트)가 표시되고 실행 중인 서비스를 볼 수 있습니다.

![](media/dcos/dcos2.png)

## Marathon UI 탐색

Marathon UI를 보려면 http://localhost/Marathon으로 이동합니다. 이 화면에서는 Azure 컨테이너 서비스 DC/OS 클러스터에 새 컨테이너 또는 다른 응용 프로그램을 시작할 수 있습니다. 컨테이너 및 응용 프로그램을 실행하는 방법에 대한 정보를 볼 수 있습니다.

![](media/dcos/dcos3.png)

## Docker로 포맷된 컨테이너 배포

Marathon을 사용하여 새 컨테이너를 배포하려면, **응용 프로그램 만들기** 단추를 클릭하고 양식에 다음 정보를 입력합니다. 준비가 되면 **응용 프로그램 만들기**를 클릭합니다.

필드 | 값
----------------|-----------
ID | nginx
이미지 | nginx
네트워크 | Bridged
호스트 포트 | 80
프로토콜 | TCP

![](media/dcos/dcos4.png)

![](media/dcos/dcos5.png)

![](media/dcos/dcos6.png)

컨테이너 포트를 에이전트의 포트에 정적으로 매핑하려면, ‘JSON Mode’를 사용하여 완료해야 합니다. 이렇게 하려면, 토글을 사용하여 새 응용 프로그램 마법사를 JSON Mode로 전환한 다음, 응용 프로그램 정의 ‘portMappings’ 섹션에 다음을 입력합니다. 이 예제는 컨테이너의 포트 80을 DC/OS 에이전트의 포트80으로 바인딩합니다. 이 마법사는 변경이 적용된 후에 JSON Mode를 빠져 나올 수 있습니다.

```none
“hostPort”: 80,
```

![](media/dcos/dcos13.png)

DC/OS 클러스터는 사설 및 공용 에이전트와 함께 배포됩니다. 인터넷에서 응용 프로그램에 액세스하려면, 공용 에이전트에 배포되어야 합니다. 이를 위해, 새 응용 프로그램 마법사의 ‘optional(선택 사항)’ 탭을 선택하고 ‘Accepted Resource Roles(수락된 리소스 역할)’에 ‘slave\_public’을 입력합니다.

![](media/dcos/dcos14.png)

다시 Marathon 주 페이지에서 컨테이너에 대한 배포 상태를 볼 수 있습니다.

![](media/dcos/dcos7.png)

DC/OS 앱(http://localhost/)으로 다시 전환하면 이 경우 Docker로 포맷된 컨테이너인 태스크가 DC/OS 클러스터에서 실행 중임이 표시됩니다.

![](media/dcos/dcos8.png)

또한 태스크가 실행되는 클러스터 노드도 볼 수 있습니다.

![](media/dcos/dcos9.png)

## 컨테이너 확장

컨테이너의 인스턴스 수를 확장하는 데 Marathon UI도 사용할 수 있습니다. 이렇게 하려면 Marathon 페이지로 이동하고 확장할 컨테이너를 선택하고 **확장** 단추를 클릭합니다. **응용 프로그램 확장** 대화 상자에서 원하는 컨테이너 인스턴스 수를 입력하고 **응용 프로그램 확장**을 선택합니다.

![](media/dcos/dcos10.png)

확장 작업이 완료된 후에 DC/OS 에이전트 전역에 분산되어 있는 동일한 태스크의 여러 인스턴스가 표시됩니다.

![](media/dcos/dcos11.png)

![](media/dcos/dcos12.png)

## 다음 단계

[DC/OS 및 Marathon API 작업](container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0525_2016-->