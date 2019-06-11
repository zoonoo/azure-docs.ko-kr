---
title: Azure의 프라이빗 Docker 컨테이너 레지스트리 - 개요
description: 클라우드 기반의 관리되는 프라이빗 Docker 레지스트리를 제공하는 Azure Container Registry 서비스에 대한 소개입니다.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ce870bfb8d29f7a808962e4d273388ab31186f10
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997410"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure의 프라이빗 Docker 컨테이너 레지스트리 소개

Azure 컨테이너 레지스트리는 오픈 소스 Docker 레지스트리 2.0에 기반한 관리되는 [Docker 레지스트리](https://docs.docker.com/registry/) 서비스입니다. 프라이빗 [Docker 컨테이너](https://www.docker.com/what-docker) 이미지를 저장하고 관리하기 위해 Azure 컨테이너 레지스트리를 만들고 유지 관리합니다.

기존 컨테이너 개발 및 배포 파이프 라인을 통해 Azure에서 컨테이너 레지스트리를 사용하거나 [ACR 작업](#azure-container-registry-tasks)을 사용하여 Azure에 컨테이너 이미지를 빌드합니다. 요청에 따라 빌드하거나 소스 코드 커밋 및 기본 이미지 업데이트 빌드 트리거를 사용하여 빌드를 완전히 자동화합니다.

Docker 및 컨테이너에 대한 배경 지식은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

## <a name="use-cases"></a>사용 사례

Azure Container Registry에서 다양한 배포 대상으로 이미지 끌어오기:

* **확장성 있는 오케스트레이션 시스템**: [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) 및 [Docker Swarm](https://docs.docker.com/swarm/)을 포함하는 호스트 클러스터에서 컨테이너화된 애플리케이션을 관리합니다.
* [AKS(Azure Kubernetes Service)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) 및 기타 서비스를 포함한 애플리케이션을 대규모로 빌드하고 실행할 수 있도록 지원하는 **Azure 서비스**.

개발자는 컨테이너 개발 워크플로의 일환으로 컨테이너 레지스트리에 밀어넣을 수도 있습니다. 예를 들어 [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) 또는 [Jenkins](https://jenkins.io/)와 같은 배포 도구 및 지속적인 통합의 컨테이너 레지스트리를 대상으로 합니다.

기본 이미지가 업데이트될 때 애플리케이션 이미지가 자동으로 다시 작성되거나 팀이 Git 리포지토리에 코드를 커밋할 때 이미지 빌드를 자동화하도록 ACR 작업을 구성합니다. 클라우드에서 여러 컨테이너 이미지를 병렬로 작성 및 테스트하고 패치를 적용하는 작업을 자동화하는 여러 단계 작업을 만듭니다.

Azure에는 Azure 컨테이너 레지스트리를 관리하는 Azure 명령줄 인터페이스, Azure Portal 및 API 지원을 비롯한 도구가 제공됩니다. 선택적으로 Azure 컨테이너 레지스트리와 작동할 [Visual Studio Code용 Docker 확장](https://code.visualstudio.com/docs/azure/docker)과 [Azure 계정](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 확장을 설정합니다. Visual Studio Code 내에서 Azure 컨테이너 레지스트리에 이미지를 밀어넣고 끌어오거나, ACR Tasks를 실행합니다.

## <a name="key-concepts"></a>주요 개념

* **레지스트리** - Azure 구독 내에서 하나 이상의 컨테이너 레지스트리를 만듭니다. 레지스트리는 세 개의 SKU, [기본, 표준 및 프리미엄](container-registry-skus.md)에서 사용할 수 있으며 각각은 웹후크 통합, Azure Active Directory와 레지스트리 인증 및 삭제 기능을 지원합니다. 컨테이너 이미지의 네트워크에 가까운 로컬 저장소의 장점을 활용하려면 배포와 동일한 Azure 위치에 레지스트리를 만듭니다. 고급 복제 및 컨테이너 이미지 배포 시나리오에 대해 프리미엄 등록의 [지역에서 복제](container-registry-geo-replication.md) 기능을 사용합니다. 정규화된 레지스트리 이름의 형식은 `myregistry.azurecr.io`입니다.

  컨테이너 레지스트리에 대한 [액세스 제어](container-registry-authentication.md)는 Azure ID, Azure Active Directory에서 지원하는 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md) 또는 제공된 관리자 계정을 사용하여 수행합니다. Azure CLI 또는 표준 `docker login` 명령을 사용하여 레지스트리에 로그인합니다.

* **리포지토리** - 레지스트리에는 이름은 같지만 태그나 다이제스트가 다른 컨테이너 이미지의 가상 그룹인 리포지토리가 하나 이상 있습니다. Azure Container Registry는 다단계 리포지토리 네임스페이스를 지원합니다. 다단계 네임스페이스를 통해 특정 앱과 관련된 이미지 컬렉션 또는 특정 배포 또는 작업 팀에 대한 앱 컬렉션을 그룹화할 수 있습니다. 예:

  * `myregistry.azurecr.io/aspnetcore:1.0.1`은 회사 차원의 이미지를 나타냅니다.
  * `myregistry.azurecr.io/warrantydept/dotnet-build`는 보증 부서 차원에서 공유되는 .NET 앱을 빌드하는 데 사용되는 이미지를 나타냅니다.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web`은 보증 부서에서 소유한 고객 제출 앱에 그룹화된 웹 이미지를 나타냅니다.

* **이미지** - 리포지토리에 저장되며 각 이미지는 Docker 호환 컨테이너의 읽기 전용 스냅숏입니다. Azure 컨테이너 레지스트리는 Windows 및 Linux 이미지 모두를 포함할 수 있습니다. 모든 컨테이너 배포에 대한 이미지 이름을 제어합니다. 표준 [Docker 명령](https://docs.docker.com/engine/reference/commandline/)을 사용하여 이미지를 리포지토리로 밀어넣거나 이미지를 리포지토리에서 끌어옵니다. Azure Container Registry는 Docker 컨테이너 이미지 외에도 [Helm 차트](container-registry-helm-repos.md)와 같은 [관련 콘텐츠 형식](container-registry-image-formats.md) 및 [OCI(Open Container Initiative) 이미지 형식 사양](https://github.com/opencontainers/image-spec/blob/master/spec.md)에 빌드된 이미지를 저장합니다.

* **컨테이너** - 컨테이너는 소프트웨어 애플리케이션을 정의하며 코드, 런타임, 시스템 도구 및 라이브러리를 포함하는 완전한 파일 시스템으로 래핑된 종속성을 정의합니다. 컨테이너 레지스트리에서 끌어온 Windows 또는 Linux 이미지를 기반으로 Docker 컨테이너를 실행합니다. 단일 컴퓨터에서 실행되는 컨테이너는 운영 체제 커널을 공유합니다. Docker 컨테이너는 모든 주요 Linux 배포판, macOS 및 Windows로 완전히 이식이 가능합니다.

## <a name="azure-container-registry-tasks"></a>Azure Container Registry 작업

[ACR 작업(Azure Container Registry 작업)](container-registry-tasks-overview.md)은 Azure에서 간편하고 효율적인 Docker 컨테이너 이미지 빌드를 제공하는 Azure Container Registry 내의 기능 모음입니다. ACR 작업을 사용하여 `docker build` 작업을 Azure로 오프로딩하면 개발 내부 루프를 클라우드로 확장할 수 있습니다. 컨테이너 OS 및 프레임워크 패치 파이프라인을 자동화하고, 팀에서 코드를 소스 제어로 커밋하면 자동으로 이미지를 빌드하도록 빌드 작업을 구성하세요.

[다중 단계 작업](container-registry-tasks-overview.md#multi-step-tasks)은 클라우드에서 컨테이너 이미지를 빌드, 테스트 및 패치하기 위한 단계 기반 작업 정의 및 실행을 지원합니다. 작업 단계는 개별 컨테이너 이미지 빌드 및 푸시 작업을 정의합니다. 해당 실행 환경으로 컨테이너를 사용하여 각 단계로 하나 이상의 컨테이너 실행을 정의할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-portal.md)
* [Azure CLI를 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)
* [ACR 작업을 사용하여 OS 및 프레임워크 패치 자동화](container-registry-tasks-overview.md)
