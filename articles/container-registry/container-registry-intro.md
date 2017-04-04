---
title: "Azure의 개인 Docker 컨테이너 레지스트리 | Microsoft Docs"
description: "클라우드 기반의 관리되는 개인 Docker 레지스트리를 제공하는 Azure Container Registry 서비스에 대한 소개입니다."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: dd504c95e22d322707c55818815b09d8a36c7ca4
ms.lasthandoff: 03/24/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>개인 Docker 컨테이너 레지스트리 소개


Azure 컨테이너 레지스트리는 오픈 소스 Docker 레지스트리 2.0에 기반한 관리되는 [Docker 레지스트리](https://docs.docker.com/registry/) 서비스입니다. 개인 [Docker 컨테이너](https://www.docker.com/what-docker) 이미지를 저장하고 관리하기 위해 Azure 컨테이너 레지스트리를 만들고 유지 관리합니다. 기존 컨테이너 배포 및 배포 파이프라인을 통해 Azure에서 컨테이너 레지스트리를 사용하고 Docker 커뮤니티 전문 기술 단체에 의지합니다.

Docker 및 컨테이너에 대한 기본 지식은 다음을 참조하세요.

* [Docker 사용자 가이드](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>사용 사례
Azure Container Registry에서 다양한 배포 대상으로 이미지 끌어오기:

* **확장성 있는 오케스트레이션 시스템**: [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) 및 [Kubernetes](http://kubernetes.io/docs/)를 포함하는 호스트 클러스터에서 컨테이너화된 응용 프로그램을 관리합니다.
* **App Service**: [Container Service](../container-service/index.md), [App Service](/app-service/index.md), [Batch](../batch/index.md) 및 [Service Fabric](../service-fabric/index.md)을 포함하는 대규모 응용 프로그램 빌드 및 실행을 지원합니다.

개발자는 컨테이너 개발 워크플로의 일환으로 컨테이너 레지스트리에 밀어넣을 수도 있습니다. 예를 들어 [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) 또는 [Jenkins](https://jenkins.io/)와 같은 배포 도구 및 지속적인 통합의 컨테이너 레지스트리를 대상으로 합니다.





## <a name="key-concepts"></a>주요 개념
* **레지스트리** - Azure 구독 내에서 하나 이상의 컨테이너 레지스트리를 만듭니다. 각 레지스트리는 같은 위치에 있는 표준 Azure [저장소 계정](../storage/storage-introduction.md)에 의해 지원됩니다. 컨테이너 이미지의 네트워크에 가까운 로컬 저장소의 장점을 활용하려면 배포와 동일한 Azure 위치에 레지스트리를 만듭니다.

  레지스트리는 구독의 [Azure Active Directory 테넌트](../active-directory/active-directory-howto-tenant.md)를 기반으로 루트 도메인에 명명됩니다. 예를 들어 Contoso 도메인에 조직 계정이 있는 경우 정규화된 레지스트리 이름은 `myregistry-contoso.azurecr.io` 형식입니다.

  컨테이너 레지스트리에 대한 [액세스 제어](container-registry-authentication.md)는 Azure Active Directory에서 지원하는 [서비스주체](../active-directory/active-directory-application-objects.md) 또는 제공된 관리자 계정을 사용하여 수행합니다. 레지스트리로 인증하려면 표준 `docker login` 명령을 실행합니다.

* **리포지토리** - 레지스트리는 컨테이너 이미지 그룹인 하나 이상의 리포지토리를 포함합니다. Azure Container Registry는 다단계 리포지토리 네임스페이스를 지원합니다. 이 기능을 통해 특정 앱과 관련된 이미지 컬렉션 또는 특정 배포 또는 작업 팀에 대한 앱 컬렉션을 그룹화할 수 있습니다. 예:

  * `myregistry.azurecr.io/aspnetcore:1.0.1`은 회사 차원의 이미지를 나타냅니다.
  * `myregistry.azurecr.io/warrantydept/dotnet-build`는 보증 부서 차원에서 공유되는 .NET 앱을 빌드하는 데 사용되는 이미지를 나타냅니다.
  * `myregistry.azrecr.io/warrantydept/customersubmissions/web`은 보증 부서에서 소유한 고객 제출 앱에 그룹화된 웹 이미지를 나타냅니다.

* **이미지** - 리포지토리에 저장되며 각 이미지는 Docker 컨테이너의 읽기 전용 스냅숏입니다. Azure 컨테이너 레지스트리는 Windows 및 Linux 이미지 모두를 포함할 수 있습니다. 모든 컨테이너 배포에 대한 이미지 이름을 제어합니다. 표준 [Docker 명령](https://docs.docker.com/engine/reference/commandline/)을 사용하여 이미지를 리포지토리로 밀어넣거나 이미지를 리포지토리에서 끌어옵니다.

* **컨테이너** - 컨테이너는 소프트웨어 응용 프로그램을 정의하며 코드, 런타임, 시스템 도구 및 라이브러리를 포함하는 완전한 파일 시스템으로 래핑된 종속성을 정의합니다. 컨테이너 레지스트리에서 끌어온 Windows 또는 Linux 이미지를 기반으로 Docker 컨테이너를 실행합니다. 단일 컴퓨터에서 실행되는 컨테이너는 운영 체제 커널을 공유합니다. Docker 컨테이너는 모든 주요 Linux 배포판, Mac 및 Windows로 완전히 이식이 가능합니다.




## <a name="next-steps"></a>다음 단계
* [Azure Portal을 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-portal.md)
* [Azure CLI를 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)
* [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)
* Visual Studio Team Services, Azure Container Service 및 Azure Container Registry를 사용하여 연속 통합 및 배포 워크플로를 작성하려면 [이 자습서](../container-service/container-service-setup-ci-cd.md)를 참조하세요.
* Azure에서 공용 끝점 없는 자체 Docker 개인 레지스트리를 설정하려면 [Azure에서 자체 개인 Docker 레지스트리 배포하기](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md)를 참조하세요.

