<properties
   pageTitle="공용 및 사용자 에이전트 풀 ACS | Microsoft Azure"
   description="공용 및 사용자 에이전트 풀이 Azure Container Service 클러스터와 함께 작동하는 방식입니다."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
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
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# 에이전트 풀

Azure Container Service는 에이전트를 여러 풀로 나눕니다. 풀에 있는 각 가상 컴퓨터가 동일하다면 각 풀에는 고유한 서브넷이 있습니다. 따라서 각 풀은 공용 또는 개인으로 표시할 수 있으며 이것은 컨테이너 서비스의 컴퓨터 간의 액세스 가능성에 영향을 줍니다.

### 사용자 에이전트

사용자 에이전트 노드는 라우팅할 수 없는 네트워크를 통해 실행됩니다. 이 네트워크는 관리 영역에서나 공용 영역 에지 라우터를 통해서만 액세스할 수 있습니다. 기본적으로 DC/OS는 사용자 에이전트 노드에서 앱을 시작합니다. 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://dcos.io/docs/1.7/administration/securing-your-cluster/)를 참조하세요.

### 공용 에이전트

공용 에이전트 노드는 공개적으로 액세스 가능한 네트워크를 통해 DC/OS 앱과 서비스를 실행합니다. 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://dcos.io/docs/1.7/administration/securing-your-cluster/)를 참조하세요.

## 에이전트 풀 사용

기본적으로 **Marathon**은 새 응용 프로그램을 *사용자* 에이전트 노드에 배포합니다. 응용 프로그램을 *공용* 노드에 명시적으로 배포해야 합니다. 아쉽게도 공용 노드로 배포하는 작업은 상자를 선택하는 것만큼 간단하지 않습니다. 새 응용 프로그램 생성 중에 **선택 사항** 탭으로 이동하고 **수락된 리소스 역할**에 **slave\_public**을 입력합니다. 이 과정은 [여기](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)와 [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 설명서에 문서화되어 있습니다.

## 다음 단계

[DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.

<!---HONumber=AcomDC_0824_2016-->